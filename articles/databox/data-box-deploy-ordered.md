---
title: Esercitazione per ordinare Azure Data Box | Microsoft Docs
description: Informazioni sui prerequisiti di distribuzione e su come ordinare un dispositivo Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: cfb95f2fb02544197f9b2796a705844e33eca201
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392481"
---
# <a name="tutorial-order-azure-data-box"></a>Esercitazione: Ordinare Azure Data Box

Azure Data Box è una soluzione ibrida che consente di importare i dati locali in Azure in modo rapido, semplice e affidabile. I dati devono essere trasferiti in un dispositivo di archiviazione da 80 TB (capacità utilizzabile) fornito da Microsoft e quindi il dispositivo deve essere rispedito a Microsoft. Questi dati vengono poi caricati in Azure.

Questa esercitazione illustra come ordinare un dispositivo Azure Data Box. Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti per la distribuzione del Data Box
> * Ordinare un Data Box
> * Monitorare l'ordine
> * Annullare l'ordine

## <a name="prerequisites"></a>Prerequisiti

# <a name="portal"></a>[Portale](#tab/portal)

Prima di distribuire il dispositivo, completare i prerequisiti di configurazione seguenti per il servizio e il dispositivo Data Box:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

È possibile accedere ad Azure ed eseguire i comandi dell'interfaccia della riga di comando di Azure in uno dei due modi seguenti:

* È possibile installare l'interfaccia della riga di comando ed eseguire i relativi comandi in locale.
* È possibile eseguire i comandi dell'interfaccia della riga di comando nel portale di Azure, in Azure Cloud Shell.

Per l'esercitazione verrà usata l'interfaccia della riga di comando di Azure tramite Windows PowerShell, ma è possibile scegliere una delle opzioni disponibili.

### <a name="install-the-cli-locally"></a>Installare l'interfaccia della riga di comando in locale

* Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), versione 2.0.67 o successiva. In alternativa, è possibile eseguire l'[installazione tramite MSI](https://aka.ms/installazurecliwindows).

#### <a name="sign-in-to-azure"></a>Accedere ad Azure

Aprire una finestra di comando di Windows PowerShell e accedere ad Azure con il comando [az login](/cli/azure/reference-index#az-login):

```azurecli
PS C:\Windows> az login
```

Di seguito è riportato l'output di un accesso riuscito:

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

#### <a name="install-the-azure-data-box-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando per Azure Data Box

Prima di poter usare i comandi dell'interfaccia della riga di comando per Azure Data Box, è necessario installare l'estensione. Le estensioni dell'interfaccia della riga di comando di Azure offrono l'accesso a comandi sperimentali e non definitivi che non sono stati ancora distribuiti come parte dell'interfaccia della riga di comando di base. Per altre informazioni sulle estensioni, vedere [Usare le estensioni con l'interfaccia della riga di comando di Azure](/cli/azure/azure-cli-extensions-overview).

Per installare l'estensione per Azure Data Box, eseguire il comando seguente: `az extension add --name databox`:

```azurecli

    PS C:\Windows> az extension add --name databox
```

Se l'installazione dell'estensione è riuscita, verrà visualizzato l'output seguente:

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

### <a name="use-azure-cloud-shell"></a>Usare Azure Cloud Shell

È possibile usare [Azure Cloud Shell](https://shell.azure.com/), un ambiente shell interattivo ospitato in Azure, tramite il browser per eseguire i comandi dell'interfaccia della riga di comando. Azure Cloud Shell supporta Bash o Windows PowerShell con i servizi di Azure. L'interfaccia della riga di comando di Azure è preinstallata e configurata per l'uso con l'account corrente. Fare clic sul pulsante Cloud Shell nel menu nella sezione superiore destra nel portale di Azure:

![Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

Il pulsante avvia una shell interattiva che è possibile usare per eseguire i passaggi riportati in questo articolo della guida pratica.

<!-- To start Azure Cloud Shell:

| Option | Example/Link |
|-----------------------------------------------|---|
| Select **Try It** in the upper-right corner of a code block. Selecting **Try It** doesn't automatically copy the code to Cloud Shell. | ![Example of Try It for Azure Cloud Shell](../../includes/media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Go to [https://shell.azure.com](https://shell.azure.com), or select the **Launch Cloud Shell** button to open Cloud Shell in your browser. | [![Launch Cloud Shell in a new window](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Select the **Cloud Shell** button on the menu bar at the upper right in the [Azure portal](https://portal.azure.com). | ![Cloud Shell button in the Azure portal](../../includes/media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

To run the code in this article in Azure Cloud Shell:

1. Start Cloud Shell.

2. Select the **Copy** button on a code block to copy the code.

3. Paste the code into the Cloud Shell session by selecting **Ctrl**+**Shift**+**V** on Windows and Linux or by selecting **Cmd**+**Shift**+**V** on macOS.

4. Select **Enter** to run the code.

For this tutorial, we use Windows PowerShell command prompt to run Azure CLI commands. -->

<!-- This goes away, we'll show this later when we show how to order a Data Box. -->
<!-- ## Change the output format type

All Azure CLI commands will use json as the output format by default unless you change it. You can change the output format by using the global parameter `--output <output-format>`. -->

<!-- ```azurecli

az databox job <command> --output <output-format>

```

Azure Data Box CLI commands support the following output formats:

* json (default setting)
* jsonc
* table
* tsv
* yaml
* yamlc
* none

You can use the parameter `--output` with all Azure Data Box CLI commands. -->

<!-- To set the output format to yaml: -->

<!-- ```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "yaml"

``` -->
<!-- 
To set the out format to tabular form (easier to read):

```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "table"

``` -->

<!-- Here's the example output of `az databox job show` after changing the output format to table:

```azurecli
PS C:\WINDOWS\system32> az databox job show --resource-group "GDPTest" --name "mydataboxtest3" --output "table"
Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.

DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
--------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
NonScheduled    True             True                       False          True                         westus      mydataboxorder  myresourcegroup          2020-06-11T22:05:49.436622+00:00  DeviceOrdered

``` -->

---

## <a name="order-data-box"></a>Ordinare il Data Box

# <a name="portal"></a>[Portale](#tab/portal)

Seguire questa procedura nel portale di Azure per ordinare un dispositivo.

1. Usare le credenziali di Microsoft Azure per accedere all'URL [https://portal.azure.com](https://portal.azure.com).
2. Fare clic su **+ Crea una risorsa** e cercare *Azure Data Box*. Fare clic su **Azure Data Box**.

   [![Cercare Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Fare clic su **Crea**.

4. Controllare se il servizio Data Box è disponibile nella propria area. Immettere o selezionare le informazioni seguenti e quindi selezionare **Applica**.

    |Impostazione  |valore  |
    |---------|---------|
    |Subscription     | Selezionare una sottoscrizione di tipo Contratto Enterprise, CSP o Azure Sponsorship per il servizio Data Box. <br> La sottoscrizione viene collegata all'account di fatturazione.       |
    |Tipo di trasferimento     | Selezionare **Importa in Azure**.        |
    |Paese/area geografica di origine    |    Selezionare il paese/area in cui si trovano attualmente i dati.         |
    |Area di Azure di destinazione     |     Selezionare l'area di Azure in cui si vogliono trasferire i dati.        |

5. Selezionare **Data Box**. La capacità massima che è possibile usare per un singolo ordine è di 80 TB. È possibile creare più ordini per volumi di dati maggiori.

      [![Selezione del Data Box - opzione 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. In **Ordine** specificare i **Dettagli ordine**. Immettere o selezionare le informazioni seguenti e quindi selezionare **Avanti**.

    |Impostazione  |valore  |
    |---------|---------|
    |Nome     |  Specificare un nome descrittivo per tenere traccia dell'ordine. <br> Il nome può contenere da 3 a 24 caratteri che possono essere lettere, numeri e trattini. <br> Il nome deve iniziare e terminare con una lettera o un numero.      |
    |Resource group     |    Usare un gruppo esistente o crearne uno nuovo. <br> Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme.         |
    |Area di Azure di destinazione     | Selezionare l'area per l'account di archiviazione. <br> Per altre informazioni, vedere [Disponibilità a livello di area](data-box-overview.md#region-availability).        |
    |Destinazione di archiviazione     | Scegliere tra account di archiviazione e/o dischi gestiti. <br> In base all'area di Azure specificata, selezionare uno o più account di archiviazione nell'elenco filtrato di un account di archiviazione esistente. Il Data Box può essere collegato a un massimo di 10 account di archiviazione. <br> È anche possibile creare un nuovo account **Utilizzo generico v1**, **Utilizzo generico v2** o un **account di archiviazione BLOB**. <br>Sono supportati gli account di archiviazione con reti virtuali. Per consentire al servizio Data Box di lavorare con gli account di archiviazione protetti, abilitare i servizi attendibili all'interno delle impostazioni del firewall di rete dell'account di archiviazione. Per altre informazioni, vedere come [Aggiungere Azure Data Box come servizio attendibile](../storage/common/storage-network-security.md#exceptions).|

    Se si usa l'account di archiviazione come destinazione, vedere lo screenshot seguente:

    ![Ordine di Data Box per l'account di archiviazione](media/data-box-deploy-ordered/order-storage-account.png)

    Se si usa Data Box per creare dischi gestiti da dischi rigidi virtuali locali è necessario fornire anche le informazioni seguenti:

    |Impostazione  |valore  |
    |---------|---------|
    |Gruppi di risorse     | Se si prevede la creazione di dischi gestiti da dischi rigidi virtuali in locale, creare nuovi gruppi di risorse. È possibile usare un gruppo di risorse solo se questo è stato creato in precedenza durante la creazione di un ordine di Data Box per il disco gestito dal servizio Data Box. <br> Specificare più gruppi di risorse separati da punti e virgola. Vengono supportati un massimo di 10 gruppi di risorse.|

    ![Ordine di Data Box per disco gestito](media/data-box-deploy-ordered/order-managed-disks.png)

    L'account di archiviazione specificato per i dischi gestiti viene usato come account di archiviazione di staging. Il servizio Data Box carica i dischi rigidi virtuali come BLOB di pagine per l'account di archiviazione di staging prima di convertirli in dischi gestiti e spostarli nei gruppi di risorse. Per altre informazioni, vedere [Verificare il caricamento dei dati in Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. In **Indirizzo di spedizione** specificare nome e cognome, nome e indirizzo postale della società e un numero di telefono valido. Fare clic su **Convalida indirizzo**. Il servizio convalida l'indirizzo di spedizione per la disponibilità del servizio. Se il servizio è disponibile per l'indirizzo di spedizione specificato, si riceve una notifica in tal senso.

   Se è stata selezionata l'opzione di spedizione autogestita, al completamento dell'ordine si riceverà una notifica tramite posta elettronica. Per altre informazioni sulla spedizione autogestita, vedere [Usare la spedizione autogestita](data-box-portal-customer-managed-shipping.md).

8. Fare clic su **Avanti** dopo la convalida dei dettagli di spedizione.

9. In **Dettagli notifica** specificare gli indirizzi di posta elettronica. Il servizio invia notifiche tramite posta elettronica per qualsiasi aggiornamento dello stato dell'ordine agli indirizzi di posta elettronica specificati.

    È consigliabile usare un indirizzo di posta elettronica di gruppo in modo da continuare a ricevere le notifiche anche se un amministratore del gruppo non è disponibile.

10. Verificare il **riepilogo** delle informazioni relative a ordine, contatti, notifiche e informativa sulla privacy. Selezionare la casella corrispondente per accettare le condizioni per la privacy.

11. Fare clic su **Ordina**. Per la creazione dell'ordine sono richiesti pochi minuti.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Seguire questa procedura nell'interfaccia della riga di comando di Azure per ordinare un dispositivo:

1. Prendere nota delle impostazioni per l'ordine di Data Box. Queste impostazioni includono le informazioni personali/aziendali, il nome della sottoscrizione, le informazioni sul dispositivo e le informazioni sulla spedizione. Sarà necessario usare queste impostazioni come parametri quando si esegue il comando dell'interfaccia della riga di comando per creare l'ordine di Data Box. Nella tabella seguente vengono illustrate le impostazioni dei parametri usate per `az databox job create`:

   | Impostazione (parametro) | Descrizione |  Valore di esempio |
   |---|---|---|
   |resource-group| Usare un gruppo esistente o crearne uno nuovo. Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. | "myresourcegroup"|
   |name| Nome dell'ordine che si sta creando. | "mydataboxorder"|
   |contact-name| Nome associato all'indirizzo di spedizione. | "Gus Poland"|
   |phone| Numero di telefono della persona o dell'azienda che riceverà l'ordine.| "14255551234"
   |posizione| Area di Azure più vicina all'utente da cui verrà spedito il dispositivo.| "Stati Uniti occidentali"|
   |sku| Dispositivo Data Box specifico che si sta ordinando. I valori validi sono: "DataBox", "DataBoxDisk" e "DataBoxHeavy"| "DataBox" |
   |email-list| Indirizzi di posta elettronica associati all'ordine.| "gusp@contoso.com" |
   |street-address1| Indirizzo a cui verrà spedito l'ordine. | "15700 NE 39th St" |
   |street-address2| Informazioni secondarie relative all'indirizzo, ad esempio numero di appartamento o numero di edificio. | "Bld 123" |
   |city| Città a cui verrà spedito il dispositivo. | "Redmond" |
   |state-or-province| Stato a cui verrà spedito il dispositivo.| "WA" |
   |country| Paese a cui verrà spedito il dispositivo. | "Stati Uniti" |
   |postal-code| Codice postale associato all'indirizzo di spedizione.| "98052"|
   |company-name| Nome dell'azienda per cui l'utente lavora.| "Contoso, LTD" |
   |archiviazione di Azure| Account di archiviazione di Azure da cui verranno importati i dati.| "mystorageaccount"|
   |debug| Include le informazioni di debug per la registrazione dettagliata  | --debug |
   |help| Visualizza le informazioni della Guida per questo comando. | --help -h |
   |only-show-errors| Mostra solo gli errori, eliminando gli avvisi. | --only-show-errors |
   |output -o| Imposta il formato di output.  Valori consentiti: json, jsonc, none, table, tsv, yaml, yamlc. Il valore predefinito è json. | --output "json" |
   |query| Stringa di query JMESPath. Per altre informazioni, vedere [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Include la registrazione dettagliata. | --verbose |

2. Nel prompt dei comandi preferito o nel terminale usare il comando [az databox job create](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create) per creare l'ordine di Azure Data Box.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Di seguito è riportato un esempio di sintassi del comando:

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. Per impostazione predefinita, tutti i comandi dell'interfaccia della riga di comando di Azure useranno JSON come formato di output, a meno che non lo si modifichi. È possibile modificare il formato di output usando il parametro globale `--output <output-format>`. La modifica del formato in "table" migliorerà la leggibilità dell'output.

   Ecco lo stesso comando appena eseguito con un piccolo perfezionamento per modificare la formattazione:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

---

## <a name="track-the-order"></a>Monitorare l'ordine

# <a name="portal"></a>[Portale](#tab/portal)

Dopo aver inserito l'ordine, è possibile monitorare lo stato dell'ordine dal portale di Azure. Passare all'ordine del Data Box e quindi a **Panoramica** per visualizzare lo stato. Il portale mostra l'ordine con stato **Ordinato**.

Se il dispositivo non è disponibile, si riceverà una notifica. Se il dispositivo è disponibile, Microsoft identifica il dispositivo per la spedizione e lo prepara. Durante la preparazione del dispositivo vengono eseguite le azioni seguenti:

* Vengono create condivisioni SMB per ogni account di archiviazione associato al dispositivo.
* Per ogni condivisione vengono generate le credenziali di accesso, come nome utente e password.
* Viene generata anche una password per il dispositivo che consente di sbloccare il dispositivo.
* Il Data Box viene bloccato per impedire qualsiasi accesso non autorizzato al dispositivo.

Una volta completata la preparazione del dispositivo, lo stato dell'ordine nel portale diventa **Elaborato**.

![Ordine del Data Box elaborato](media/data-box-overview/data-box-order-status-processed.png)

Microsoft prepara e spedisce quindi il dispositivo tramite un corriere locale. Si riceverà un numero di tracciabilità dopo la spedizione del dispositivo. Il portale mostra l'ordine con lo stato **Spedito**.

![Ordine del Data Box spedito](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

### <a name="track-a-single-order"></a>Monitorare un singolo ordine

Per ottenere le informazioni di tracciabilità su un singolo ordine di Azure Data Box esistente, eseguire [az databox job show](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-show). Il comando visualizza le informazioni sull'ordine, ad esempio: nome, gruppo di risorse, informazioni di tracciabilità, ID sottoscrizione, informazioni di contatto, tipo di spedizione e SKU del dispositivo.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   Nella tabella seguente vengono illustrate le informazioni sui parametri per `az databox job show`:

   | Parametro | Descrizione |  Valore di esempio |
   |---|---|---|
   |resource-group [obbligatorio]| Nome del gruppo di risorse associato all'ordine. Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. | "myresourcegroup"|
   |name [obbligatorio]| Nome dell'ordine da visualizzare. | "mydataboxorder"|
   |debug| Include le informazioni di debug per la registrazione dettagliata | --debug |
   |help| Visualizza le informazioni della Guida per questo comando. | --help -h |
   |only-show-errors| Mostra solo gli errori, eliminando gli avvisi. | --only-show-errors |
   |output -o| Imposta il formato di output.  Valori consentiti: json, jsonc, none, table, tsv, yaml, yamlc. Il valore predefinito è json. | --output "json" |
   |query| Stringa di query JMESPath. Per altre informazioni, vedere [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Include la registrazione dettagliata. | --verbose |

   Di seguito è riportato un esempio del comando con formato di output impostato su "table":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> L'elenco degli ordini può essere supportato a livello di sottoscrizione e, in tal caso, il gruppo di risorse diventa un parametro facoltativo, anziché un parametro obbligatorio.

### <a name="list-all-orders"></a>Elencare tutti gli ordini

Se sono stati ordinati più dispositivi, è possibile eseguire [az databox job list](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-list) per visualizzare tutti gli ordini di Azure Data Box. Il comando elenca tutti gli ordini che appartengono a un gruppo di risorse specifico. Nell'output sono visualizzate anche le informazioni seguenti: nome dell'ordine, stato di spedizione, area di Azure, tipo di recapito, stato dell'ordine. Nell'elenco sono anche inclusi gli ordini annullati.
Il comando visualizza inoltre i timestamp di ogni ordine.

```azurecli
az databox job list --resource-group <resource-group>
```

Nella tabella seguente vengono illustrate le informazioni sui parametri per `az databox job list`:

   | Parametro | Descrizione |  Valore di esempio |
   |---|---|---|
   |resource-group [obbligatorio]| Nome del gruppo di risorse contenente l'ordine. Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. | "myresourcegroup"|
   |debug| Include le informazioni di debug per la registrazione dettagliata | --debug |
   |help| Visualizza le informazioni della Guida per questo comando. | --help -h |
   |only-show-errors| Mostra solo gli errori, eliminando gli avvisi. | --only-show-errors |
   |output -o| Imposta il formato di output.  Valori consentiti: json, jsonc, none, table, tsv, yaml, yamlc. Il valore predefinito è json. | --output "json" |
   |query| Stringa di query JMESPath. Per altre informazioni, vedere [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Include la registrazione dettagliata. | --verbose |

   Di seguito è riportato un esempio del comando con formato di output impostato su "table":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        GDPTest          2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  GDPTest          2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       GDPTest          2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       GDPTest          2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       GDPTest          2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

---

## <a name="cancel-the-order"></a>Annullare l'ordine

# <a name="portal"></a>[Portale](#tab/portal)

Per annullare l'ordine, nel portale di Azure passare a **Panoramica** e fare clic su **Annulla** sulla barra dei comandi.

Dopo aver effettuato un ordine, è possibile annullarlo in qualsiasi momento prima che il suo stato venga contrassegnato come elaborato.

Per eliminare un ordine annullato, passare a **Panoramica** e fare clic su **Elimina** sulla barra dei comandi.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

### <a name="cancel-an-order"></a>Annullare un ordine

Per annullare un ordine di Azure Data Box, eseguire [az databox job cancel](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-cancel). È necessario specificare il motivo per l'annullamento dell'ordine.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   Nella tabella seguente vengono illustrate le informazioni sui parametri per `az databox job cancel`:

   | Parametro | Descrizione |  Valore di esempio |
   |---|---|---|
   |resource-group [obbligatorio]| Nome del gruppo di risorse associato all'ordine da eliminare. Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. | "myresourcegroup"|
   |name [obbligatorio]| Nome dell'ordine da eliminare. | "mydataboxorder"|
   |reason [obbligatorio]| Motivo per l'annullamento dell'ordine. | "Ho immesso informazioni errate e ho dovuto annullare l'ordine." |
   |sì| Indica che non è richiesta la conferma. | --yes (-y)| --yes -y |
   |debug| Include le informazioni di debug per la registrazione dettagliata | --debug |
   |help| Visualizza le informazioni della Guida per questo comando. | --help -h |
   |only-show-errors| Mostra solo gli errori, eliminando gli avvisi. | --only-show-errors |
   |output -o| Imposta il formato di output.  Valori consentiti: json, jsonc, none, table, tsv, yaml, yamlc. Il valore predefinito è json. | --output "json" |
   |query| Stringa di query JMESPath. Per altre informazioni, vedere [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Include la registrazione dettagliata. | --verbose |

   Di seguito è riportato un esempio del comando con l'output:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Eliminare un ordine

Se è stato annullato un ordine di Azure Data Box, è possibile eseguire [az databox job delete](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-delete) per eliminare l'ordine.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   Nella tabella seguente vengono illustrate le informazioni sui parametri per `az databox job delete`:

   | Parametro | Descrizione |  Valore di esempio |
   |---|---|---|
   |resource-group [obbligatorio]| Nome del gruppo di risorse associato all'ordine da eliminare. Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. | "myresourcegroup"|
   |name [obbligatorio]| Nome dell'ordine da eliminare. | "mydataboxorder"|
   |sottoscrizione| Nome o ID (GUID) della propria sottoscrizione di Azure. | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |sì| Indica che non è richiesta la conferma. | --yes (-y)| --yes -y |
   |debug| Include le informazioni di debug per la registrazione dettagliata | --debug |
   |help| Visualizza le informazioni della Guida per questo comando. | --help -h |
   |only-show-errors| Mostra solo gli errori, eliminando gli avvisi. | --only-show-errors |
   |output -o| Imposta il formato di output.  Valori consentiti: json, jsonc, none, table, tsv, yaml, yamlc. Il valore predefinito è json. | --output "json" |
   |query| Stringa di query JMESPath. Per altre informazioni, vedere [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Include la registrazione dettagliata. | --verbose |

Di seguito è riportato un esempio del comando con l'output:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

---

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad articoli di Azure Data Box, ad esempio:

> [!div class="checklist"]
>
> * Prerequisiti per la distribuzione del Data Box
> * Ordinare il Data Box
> * Monitorare l'ordine
> * Annullare l'ordine

Passare all'esercitazione successiva per informazioni su come configurare il Data Box.

> [!div class="nextstepaction"]
> [Configurare Azure Data Box](./data-box-deploy-set-up.md)
