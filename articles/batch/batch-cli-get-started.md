---
title: Introduzione all'interfaccia della riga di comando di Azure per Batch | Microsoft Docs
description: Introduzione rapida ai comandi per Batch nell'interfaccia della riga di comando di Azure per la gestione delle risorse del servizio Azure Batch
services: batch
documentationcenter: 
author: v-dotren
manager: timlt
editor: 
ms.assetid: fcd76587-1827-4bc8-a84d-bba1cd980d85
ms.service: batch
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 09/28/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68a5493282fa4a0b54ba551c48ae963a42b94dca
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-batch-resources-with-azure-cli"></a>Gestire le risorse di Batch con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure 2.0 è la nuova esperienza della riga di comando di Azure per gestire le risorse di Azure. Può essere usata in macOS, Linux e Windows. L'interfaccia della riga di comando di Azure 2.0 è ottimizzata per la gestione e l'amministrazione delle risorse di Azure dalla riga di comando. Si può usare l'interfaccia della riga di comando di Azure per gestire gli account Azure Batch e le risorse, come pool, processi e attività. Con l'interfaccia della riga di comando di Azure è possibile creare script per molte delle attività eseguite con le API Batch, il portale di Azure e i cmdlet di PowerShell per Batch.

Questo articolo offre una panoramica dell'uso dell'[interfaccia della riga di comando di Azure versione 2.0](https://docs.microsoft.com/cli/azure/overview) con Batch. Per una panoramica dell'uso dell'interfaccia della riga di comando con Azure, vedere [Introduzione all'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

È consigliabile usare la versione più recente dell'interfaccia della riga di comando di Azure 2.0. Per altre informazioni sulla versione 2.0, vedere l'articolo relativo alla [disponibilità generale dell'interfaccia della riga di comando di Azure 2.0](https://azure.microsoft.com/blog/announcing-general-availability-of-vm-storage-and-network-azure-cli-2-0/).

## <a name="set-up-the-azure-cli"></a>Configurare l'interfaccia della riga di comando di Azure

Per installare l'interfaccia della riga di comando di Azure, seguire la procedura illustrata nell'articolo [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

> [!TIP]
> È consigliabile aggiornare frequentemente l'installazione dell'interfaccia della riga di comando di Azure per sfruttare i vantaggi degli aggiornamenti e dei miglioramenti del servizio.
> 
> 

## <a name="command-help"></a>Guida per i comandi

È possibile visualizzare il testo della Guida per ogni comando dell'interfaccia della riga di comando di Azure aggiungendo `-h` al comando. Omettere qualsiasi altra opzione. ad esempio:

* Per visualizzare la Guida per il comando `az`, immettere `az -h`
* Per ottenere un elenco di tutti i comandi per Batch nell'interfaccia della riga di comando, usare `az batch -h`
* Per visualizzare la Guida in merito alla creazione di un account Batch, immettere `az batch account create -h`

In caso di dubbi, usare l'opzione della riga di comando `-h` per visualizzare la Guida per qualsiasi comando dell'interfaccia della riga di comando di Azure.

> [!NOTE]
> Nelle versioni precedenti dell'interfaccia della riga di comando di Azure si usa `azure` anteposto al comando dell'interfaccia della riga di comando. Nella versione 2.0 tutti i comandi ora sono preceduti da `az`. Assicurarsi di aggiornare gli script per l'uso della nuova sintassi con la versione 2.0.
>
>  

Per informazioni dettagliate sui [comandi dell'interfaccia della riga di comando di Azure per Batch](https://docs.microsoft.com/cli/azure/batch), vedere la documentazione di riferimento dell'interfaccia della riga di comando di Azure. 

## <a name="log-in-and-authenticate"></a>Accesso e autenticazione

Per usare l'interfaccia della riga di comando di Azure con Batch, è necessario accedere ed eseguire l'autenticazione. La procedura prevede due semplici passaggi:

1. **Accedere ad Azure.** L'accesso ad Azure permette di accedere ai comandi di Azure Resource Manager, tra cui i comandi del [servizio di gestione Batch](batch-management-dotnet.md).  
2. **Accedere all'account Batch.** L'accesso all'account Batch permette di accedere ai comandi del servizio Batch.   

### <a name="log-in-to-azure"></a>Accedere ad Azure

Per accedere ad Azure è possibile procedere in diversi modi, descritti nel dettaglio nell'articolo [Accedere con l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli):

1. [Accedere modo interattivo](https://docs.microsoft.com/cli/azure/authenticate-azure-cli#az_authenticate_azure_cli_interactive_log_in). Accedere in modo interattivo quando si eseguono comandi dell'interfaccia della riga di comando di Azure direttamente dalla riga di comando.
2. [Accedere con un'entità servizio](https://docs.microsoft.com/cli/azure/authenticate-azure-cli#az_authenticate_azure_cli_logging_in_with_a_service_principal). Accedere con un'entità servizio quando si eseguono comandi dell'interfaccia della riga di comando di Azure da uno script o un'applicazione.

Ai fini di questo articolo, viene illustrato come accedere ad Azure in modo interattivo. Digitare [az login](https://docs.microsoft.com/cli/azure/#login) nella riga di comando:

```azurecli
# Log in to Azure and authenticate interactively.
az login
```

Il comando `az login` restituisce un token che può essere usato per eseguire l'autenticazione, come illustrato di seguito. Seguire le istruzioni fornite per aprire una pagina Web e inviare il token ad Azure:

![Accedere ad Azure](./media/batch-cli-get-started/az-login.png)

Gli esempi riportati nella sezione [Script della shell di esempio](#sample-shell-scripts) mostrano anche come avviare la sessione dell'interfaccia della riga di comando di Azure eseguendo l'accesso ad Azure in modo interattivo. Dopo aver eseguito l'accesso, è possibile chiamare i comandi per usare le risorse di gestione Batch, inclusi i pacchetti di applicazioni, le chiavi, le quote e gli account Batch.  

### <a name="log-in-to-your-batch-account"></a>Accedere all'account Batch

Per usare l'interfaccia della riga di comando di Azure per gestire le risorse di Batch, come pool, processi e attività, è necessario accedere all'account Batch ed eseguire l'autenticazione. Per accedere al servizio Batch, usare il comando [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login). 

Per l'autenticazione con l'account Batch è possibile procedere in due modi:

- **Tramite l'autenticazione con Azure Active Directory (Azure AD).** 

    L'autenticazione con Azure AD è l'opzione predefinita quando si usa l'interfaccia della riga di comando di Azure con Batch ed è consigliata per la maggior parte degli scenari. 
    
    Quando si accede ad Azure in modo interattivo, come descritto nella sezione precedente, le credenziali vengono memorizzate nella cache per accedere tramite l'interfaccia della riga di comando di Azure al proprio account Batch usando le stesse credenziali. Se si accede ad Azure usando un'entità servizio, tali credenziali vengono usate anche per accedere all'account Batch.

    Uno dei vantaggi di Azure AD è che offre il controllo degli accessi in base al ruolo. Con il controllo degli accessi in base al ruolo, l'accesso dell'utente dipende dal ruolo assegnato anziché dal fatto che abbia o meno le chiavi dell'account. Anziché gestire le chiavi dell'account, è possibile gestire i ruoli del controllo degli accessi in base al ruolo e lasciare la gestione dell'accesso e dell'autenticazione ad Azure AD.  

        To log in to your Batch account using Azure AD, call the [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) command: 

    ```azurecli
    az batch account login -g myresource group -n mybatchaccount
    ```

- **Tramite l'autenticazione con chiave condivisa.**

    [L'autenticazione con chiave condivisa](https://docs.microsoft.com/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service#authentication-via-shared-key) usa le chiavi di accesso dell'account per autenticare i comandi dell'interfaccia della riga di comando di Azure per il servizio Batch.

    Se si creano script dell'interfaccia della riga di comando di Azure per automatizzare la chiamata di comandi di Batch, è possibile usare l'autenticazione con chiave condivisa o un'entità servizio di Azure AD. In alcuni scenari potrebbe essere più semplice usare l'autenticazione con chiave condivisa anziché creare un'entità servizio.  

    Per accedere usando l'autenticazione con chiave condivisa, includere l'opzione `--shared-key-auth` nella riga di comando:

    ```azurecli
    az batch account login -g myresourcegroup -n mybatchaccount --shared-key-auth
    ```

Gli esempi riportati nella sezione [Script della shell di esempio](#sample-shell-scripts) mostrano come accedere all'account Batch con l'interfaccia della riga di comando di Azure usando sia Azure AD che la chiave condivisa.

## <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Usare il trasferimento di file e i modelli dell'interfaccia della riga di comando di Azure Batch (anteprima)

È possibile usare l'interfaccia della riga di comando di Azure per eseguire processi di Batch end-to-end senza scrivere codice. I file di modello del servizio Batch supportano la creazione di pool, processi e attività con l'interfaccia della riga di comando di Azure. È anche possibile usare l'interfaccia della riga di comando di Azure per caricare file di input dei processi nell'account di archiviazione di Azure associato all'account Batch e quindi scaricare file di output dei processi da tale interfaccia. Per altre informazioni, vedere [Usare il trasferimento di file e i modelli dell'interfaccia della riga di comando di Azure Batch (anteprima)](batch-cli-templates.md).

## <a name="sample-shell-scripts"></a>Script della shell di esempio

Gli script di esempio riportati nella tabella seguente mostrano come usare i comandi dell'interfaccia della riga di comando di Azure con il servizio Batch e il servizio di gestione Batch per eseguire attività comuni. Questi script di esempio illustrano molti dei comandi disponibili nell'interfaccia della riga di comando di Azure per Batch. 

| Script | Note |
|---|---|
| [Creare un account Batch](./scripts/batch-cli-sample-create-account.md) | Crea un account Batch e lo associa a un account di archiviazione. |
| [Aggiungere un'applicazione](./scripts/batch-cli-sample-add-application.md) | Aggiunge un'applicazione e carica i file binari nel pacchetto.|
| [Gestire i pool di Batch](./scripts/batch-cli-sample-manage-pool.md) | Illustra la creazione, il ridimensionamento e la gestione dei pool. |
| [Eseguire un processo e le attività con Batch](./scripts/batch-cli-sample-run-job.md) | Illustra l'esecuzione di un processo e l'aggiunta di attività. |

## <a name="json-files-for-resource-creation"></a>File JSON per la creazione di risorse

Quando si creano risorse di Batch come pool e processi, è possibile specificare un file JSON contenente la configurazione della nuova risorsa anziché passarne i parametri come opzioni della riga di comando. ad esempio:

```azurecli
az batch pool create my_batch_pool.json
```

Anche se è possibile creare la maggior parte delle risorse Batch usando solo opzioni della riga di comando, per alcune funzionalità è necessario specificare un file in formato JSON contenente i dettagli delle risorse. È necessario usare un file JSON, ad esempio, se si vogliono specificare file di risorse per un'attività di avvio.

Per la sintassi JSON necessaria per creare una risorsa, vedere la documentazione di [riferimento sull'API REST di Batch][rest_api]. Ogni argomento "Aggiungi *tipo di risorsa*" nelle informazioni di riferimento sull'API REST contiene script JSON di esempio per la creazione di tale risorsa. È possibile usare gli script JSON di esempio come modelli per i file JSON da usare con l'interfaccia della riga di comando di Azure. Per la sintassi JSON necessaria per la creazione di pool, vedere [Add a pool to an account][rest_add_pool] (Aggiungere un pool a un account).

Per uno script di esempio che specifica un file JSON, vedere [Eseguire processi in Azure Batch con l'interfaccia della riga di comando di Azure](./scripts/batch-cli-sample-run-job.md).

> [!NOTE]
> Se si specifica un file JSON durante la creazione di una risorsa, gli altri parametri specificati nella riga di comando per la risorsa vengono ignorati.
> 
> 

## <a name="efficient-queries-for-batch-resources"></a>Query efficienti per le risorse Batch

Ogni tipo di risorsa di Batch supporta un comando `list` che esegue query sull'account Batch ed elenca le risorse di tale tipo. È ad esempio possibile elencare i pool dell'account e le attività di un processo:

```azurecli
az batch pool list
az batch task list --job-id job001
```

Quando si esegue una query sul servizio Batch con un'operazione `list`, è possibile specificare una clausola OData per limitare la quantità di dati restituiti. Dato che tutte le operazioni di filtro vengono eseguite sul lato server, vengono restituiti solo i dati richiesti. Queste clausole consentono di risparmiare larghezza di banda (e quindi tempo) quando si eseguono operazioni di tipo elenco.

La tabella seguente illustra le clausole OData supportate dal servizio Batch:

| Clausola | Descrizione |
|---|---|
| `--select-clause [select-clause]` | Restituisce un subset di proprietà per ogni entità. |
| `--filter-clause [filter-clause]` | Restituisce solo le entità che corrispondono all'espressione OData specificata. |
| `--expand-clause [expand-clause]` | Ottiene le informazioni sull'entità in una singola chiamata REST sottostante. La clausola expand supporta attualmente solo la proprietà `stats`. |

Per uno script di esempio che illustra come usare la clausola OData, vedere [Eseguire processi in Azure Batch con l'interfaccia della riga di comando di Azure](./scripts/batch-cli-sample-run-job.md).

Per altre informazioni sull'esecuzione di query di elenco efficienti con clausole OData, vedere [Creare query per elencare le risorse di Batch in modo efficiente](batch-efficient-list-queries.md).

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

I suggerimenti riportati di seguito possono contribuire alla risoluzione di problemi dell'interfaccia della riga di comando di Azure:

* Usare `-h` per visualizzare il **testo della Guida** per qualsiasi comando dell'interfaccia della riga di comando.
* Usare `-v` e `-vv` per visualizzare l'output del comando **verbose**. Quando il flag `-vv` è incluso, l'interfaccia della riga di comando di Azure visualizza le richieste REST e le risposte effettive. Queste opzioni sono utili per visualizzare l'output completo degli errori.
* È possibile visualizzare l'**output del comando in formato JSON** con l'opzione `--json`. Ad esempio, `az batch pool show pool001 --json` visualizza le proprietà di pool001 in formato JSON. È quindi possibile copiare e modificare tale output per l'uso in un `--json-file` (vedere [File JSON](#json-files) più indietro in questo articolo).
<!---Loc Comment: Please, check link [JSON files] since it's not redirecting to any location.--->
* Il [forum di Batch][batch_forum] è monitorato dai membri del team di Batch. Se si riscontrano problemi o si vuole assistenza per un'operazione specifica, è possibile pubblicare le proprie domande nel forum.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).
* Per altre informazioni sulle risorse Batch, vedere la [panoramica di Azure Batch per gli sviluppatori](batch-api-basics.md).
* Per altre informazioni sull'uso dei modelli del servizio Batch per creare pool processi e attività senza scrivere codice, vedere [Usare il trasferimento di file e i modelli dell'interfaccia della riga di comando di Azure Batch (anteprima)](batch-cli-templates.md).

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
