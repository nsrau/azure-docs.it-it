---
title: Eseguire il mapping di un dominio personalizzato a un endpoint di Archiviazione BLOB di Azure
titleSuffix: Azure Storage
description: Eseguire il mapping di un dominio personalizzato a un archivio BLOB o a un endpoint Web in un account di archiviazione di Azure.Map a custom domain to a Blob Storage or web endpoint in an Azure storage account.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 9d05677ec47851557594ef47499da653accad141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370475"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Eseguire il mapping di un dominio personalizzato a un endpoint di Archiviazione BLOB di Azure

È possibile eseguire il mapping di un dominio personalizzato a un endpoint del servizio BLOB o a un endpoint [di sito Web statico.](storage-blob-static-website.md) 

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> Questo mapping funziona solo per i `www.contoso.com`sottodomini (ad esempio: ). Se si vuole che l'endpoint Web sia disponibile `contoso.com`nel dominio radice , ad esempio: ), sarà necessario usare la rete CDN di Azure.If you want your web endpoint to be available on the root domain (for example: ), then you'll have to use Azure CDN. Per istruzioni, vedere la sezione [Mappare un dominio personalizzato con HTTPS abilitato](#enable-https) di questo articolo. Poiché la sezione di questo articolo è relativa alla pagina per abilitare il dominio radice del dominio personalizzato, il passaggio all'interno di tale sezione per l'abilitazione di HTTPS è facoltativo. 

<a id="enable-http" />

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Eseguire il mapping di un dominio personalizzato con solo HTTP abilitato

Questo approccio è più semplice, ma consente solo l'accesso HTTP. Se l'account di archiviazione è configurato per richiedere il [trasferimento sicuro](../common/storage-require-secure-transfer.md) su HTTPS, è necessario abilitare l'accesso HTTPS per il dominio personalizzato. 

Per abilitare l'accesso HTTPS, vedere la sezione [Mappare un dominio personalizzato con HTTPS abilitato](#enable-https) di questo articolo. 

<a id="map-a-domain" />

### <a name="map-a-custom-domain"></a>Eseguire il mapping di un dominio personalizzato

> [!IMPORTANT]
> Il dominio personalizzato non sarà brevemente disponibile per gli utenti durante il completamento della configurazione. Se il dominio supporta attualmente un'applicazione con un contratto di servizio (SLA) che non richiede tempi di inattività, seguire i passaggi nella sezione [Mappare un dominio personalizzato con tempo](#zero-down-time) di inattività pari a zero di questo articolo per garantire che gli utenti possano accedere al dominio mentre viene eseguito il mapping DNS.

Se non si teme che il dominio non sia brevemente disponibile per gli utenti, attenersi alla seguente procedura.

:heavy_check_mark: Passaggio 1: Ottenere il nome host dell'endpoint di archiviazione.

:heavy_check_mark: Passaggio 2: Creare un record di nome canonico (CNAME) con il provider di dominio.

:heavy_check_mark: Passaggio 3: Registrare il dominio personalizzato con Azure.:heavy_check_mark: Step 3: Register the custom domain with Azure. 

:heavy_check_mark: Passaggio 4: Testare il dominio personalizzato.

<a id="endpoint" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Passaggio 1: Ottenere il nome host dell'endpoint di archiviazioneStep 1: Get the host name of your storage endpoint 

Il nome host è l'URL dell'endpoint di archiviazione senza l'identificatore di protocollo e la barra finale. 

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

2. Nel riquadro dei menu, in **Impostazioni**, selezionare **Proprietà**.  

3. Copiare il valore dell'endpoint del **servizio BLOB primario** o dell'endpoint **del sito Web statico primario** in un file di testo. 

4. Rimuovere l'identificatore di protocollo *(ad esempio,* HTTPS) e la barra finale da tale stringa. La tabella seguente contiene esempi.

   | Tipo di endpoint |  endpoint | nome host |
   |------------|-----------------|-------------------|
   |servizio BLOB  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |sito Web statico  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Impostare questo valore per un secondo momento.

<a id="create-cname-record" />

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Passaggio 2: Creare un record di nome canonico (CNAME) con il provider di dominioStep 2: Create a canonical name (CNAME) record with your domain provider

Creare un record CNAME che punti al nome host. Un record CNAME è un tipo di record DNS che esegue il mapping di un nome di dominio di origine a uno di destinazione.

1. Accedere al sito Web del registrar e quindi passare alla pagina per la gestione dell'impostazione DNS.

   che potrebbe essere contenuta in una sezione denominata **Nome di dominio**, **DNS** o **Gestione server dei nomi**.

2. Individuare la sezione relativa alla gestione dei record CNAME. 

   Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME**, **Alias** o **Subdomains**.

3. Creare un record CNAME. Come parte di tale record, fornire i seguenti elementi: 

   - Alias del sottodominio, ad `www` esempio o `photos`. Il sottodominio è obbligatorio, i domini radice non sono supportati. 
      
   - Il nome host ottenuto nella sezione [Ottenere il nome host dell'endpoint di archiviazione](#endpoint) più indietro in questo articolo. 

<a id="register" />

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Passaggio 3: Registrare il dominio personalizzato con AzureStep 3: Register your custom domain with Azure

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

2. Nel riquadro del menu in **Servizio BLOB** selezionare **Dominio personalizzato**.  

   ![opzione di dominio personalizzato](./media/storage-custom-domain-name/custom-domain-button.png "dominio personalizzato")

   Verrà visualizzato il riquadro **Dominio personalizzato**.

3. Nella casella di testo **Nome dominio** immettere il nome del dominio personalizzato, incluso il sottodominio  
   
   Ad esempio, se il dominio è *contoso.com* e `www.contoso.com`l'alias del sottodominio è *www*, immettere . Se il sottodominio è `photos.contoso.com` *foto*, immettere .

4. Per registrare il dominio personalizzato, scegliere il pulsante **Salva.**

   Dopo che il record CNAME è stato propagato tramite i Domain Name Server (DNS) e se gli utenti dispongono delle autorizzazioni appropriate, possono visualizzare i dati BLOB usando il dominio personalizzato.

#### <a name="step-4-test-your-custom-domain"></a>Passaggio 4: Testare il dominio personalizzatoStep 4: Test your custom domain

Per verificare che il mapping del dominio personalizzato all'endpoint del servizio BLOB sia stato eseguito, creare un BLOB in un contenitore pubblico all'interno dell'account di archiviazione. Quindi, in un Web browser, accedere al BLOB usando un URI nel formato seguente: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Per accedere a un modulo Web nel contenitore *myforms* nel sottodominio personalizzato *photos.contoso.com*, è possibile ad esempio usare l'URI seguente: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time" />

### <a name="map-a-custom-domain-with-zero-downtime"></a>Eseguire il mapping di un dominio personalizzato con nessun tempo di inattività

> [!NOTE]
> Se non si è interessati al fatto che il dominio non sia brevemente disponibile per gli utenti, è consigliabile seguire la procedura descritta nella sezione [Mappare un dominio personalizzato](#map-a-domain) di questo articolo. È un approccio più semplice con meno passaggi.  

Se il dominio supporta attualmente un'applicazione con un contratto di servizio (SLA) che non richiede tempi di inattività, attenersi alla seguente procedura per assicurarsi che gli utenti possano accedere al dominio durante il mapping DNS. 

:heavy_check_mark: Passaggio 1: Ottenere il nome host dell'endpoint di archiviazione.

:heavy_check_mark: Passaggio 2: Creare un record di nome canonico intermedio (CNAME) con il provider di dominio.

:heavy_check_mark: Passaggio 3: Preregistrare il dominio personalizzato con Azure.:heavy_check_mark: Step 3: Pre-register the custom domain with Azure.

:heavy_check_mark: Passaggio 4: Creare un record CNAME con il provider di dominio.

:heavy_check_mark: Passaggio 5: Testare il dominio personalizzato.

<a id="endpoint-2" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Passaggio 1: Ottenere il nome host dell'endpoint di archiviazioneStep 1: Get the host name of your storage endpoint 

Il nome host è l'URL dell'endpoint di archiviazione senza l'identificatore di protocollo e la barra finale. 

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

2. Nel riquadro dei menu, in **Impostazioni**, selezionare **Proprietà**.  

3. Copiare il valore dell'endpoint del **servizio BLOB primario** o dell'endpoint **del sito Web statico primario** in un file di testo. 

4. Rimuovere l'identificatore di protocollo *(ad esempio,* HTTPS) e la barra finale da tale stringa. La tabella seguente contiene esempi.

   | Tipo di endpoint |  endpoint | nome host |
   |------------|-----------------|-------------------|
   |servizio BLOB  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |sito Web statico  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Impostare questo valore per un secondo momento.

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Passaggio 2: Creare un record di nome canonico intermedio (CNAME) con il provider di dominioStep 2: Create a intermediary canonical name (CNAME) record with your domain provider

Creare un record CNAME temporaneo che punti al nome host. Un record CNAME è un tipo di record DNS che esegue il mapping di un nome di dominio di origine a uno di destinazione.

1. Accedere al sito Web del registrar e quindi passare alla pagina per la gestione dell'impostazione DNS.

   che potrebbe essere contenuta in una sezione denominata **Nome di dominio**, **DNS** o **Gestione server dei nomi**.

2. Individuare la sezione relativa alla gestione dei record CNAME. 

   Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME**, **Alias** o **Subdomains**.

3. Creare un record CNAME. Come parte di tale record, fornire i seguenti elementi: 

   - Alias del sottodominio, ad `www` esempio o `photos`. Il sottodominio è obbligatorio, i domini radice non sono supportati.

     Aggiungere `asverify` il sottodominio all'alias. Ad esempio, `asverify.www` o `asverify.photos`.
       
   - Il nome host ottenuto nella sezione [Ottenere il nome host dell'endpoint di archiviazione](#endpoint) più indietro in questo articolo. 

     Aggiungere il `asverify` sottodominio al nome host. Ad esempio `asverify.mystorageaccount.blob.core.windows.net`.

4. Per registrare il dominio personalizzato, scegliere il pulsante **Salva.**

   Se la registrazione ha esito positivo, una notifica nel portale informa che l'account di archiviazione è stato aggiornato correttamente. Il dominio personalizzato è stato verificato da Azure, ma il traffico verso il dominio non è ancora instradato all'account di archiviazione.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Passaggio 3: Preregistrare il dominio personalizzato con AzureStep 3: Pre-register your custom domain with Azure

Quando si preregistra il dominio personalizzato con Azure, si consente ad Azure di riconoscere il dominio personalizzato senza dover modificare il record DNS per il dominio. In questo modo, quando si modifica il record DNS per il dominio, verrà eseguito il mapping all'endpoint BLOB senza tempi di inattività.

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

2. Nel riquadro del menu in **Servizio BLOB** selezionare **Dominio personalizzato**.  

   ![opzione di dominio personalizzato](./media/storage-custom-domain-name/custom-domain-button.png "dominio personalizzato")

   Verrà visualizzato il riquadro **Dominio personalizzato**.

3. Nella casella di testo **Nome dominio** immettere il nome del dominio personalizzato, incluso il sottodominio  
   
   Ad esempio, se il dominio è *contoso.com* e `www.contoso.com`l'alias del sottodominio è *www*, immettere . Se il sottodominio è `photos.contoso.com` *foto*, immettere .

4. Selezionare la casella di controllo **Usa convalida CNAME indiretta**.

5. Per registrare il dominio personalizzato, scegliere il pulsante **Salva.**
  
   Dopo che il record CNAME è stato propagato tramite i Domain Name Server (DNS) e se gli utenti dispongono delle autorizzazioni appropriate, possono visualizzare i dati BLOB usando il dominio personalizzato.

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Passaggio 4: Creare un record CNAME con il provider di dominioStep 4: Create a CNAME record with your domain provider

Creare un record CNAME temporaneo che punti al nome host.

1. Accedere al sito Web del registrar e quindi passare alla pagina per la gestione dell'impostazione DNS.

   che potrebbe essere contenuta in una sezione denominata **Nome di dominio**, **DNS** o **Gestione server dei nomi**.

2. Individuare la sezione relativa alla gestione dei record CNAME. 

   Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME**, **Alias** o **Subdomains**.

3. Creare un record CNAME. Come parte di tale record, fornire i seguenti elementi: 

   - Alias del sottodominio, ad `www` esempio o `photos`. Il sottodominio è obbligatorio, i domini radice non sono supportati.
      
   - Il nome host ottenuto nella sezione [Ottenere il nome host dell'endpoint di archiviazione](#endpoint-2) più indietro in questo articolo. 

#### <a name="step-5-test-your-custom-domain"></a>Passaggio 5: Testare il dominio personalizzatoStep 5: Test your custom domain

Per verificare che il mapping del dominio personalizzato all'endpoint del servizio BLOB sia stato eseguito, creare un BLOB in un contenitore pubblico all'interno dell'account di archiviazione. Quindi, in un Web browser, accedere al BLOB usando un URI nel formato seguente: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Per accedere a un modulo Web nel contenitore *myforms* nel sottodominio personalizzato *photos.contoso.com*, è possibile ad esempio usare l'URI seguente: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Rimuovere un mapping di dominio personalizzatoRemove a custom domain mapping

Per rimuovere un mapping di dominio personalizzato, annullare la registrazione del dominio personalizzato. Utilizzare una delle seguenti procedure.

#### <a name="portal"></a>[Portale](#tab/azure-portal)

Per rimuovere l'impostazione di dominio personalizzato, eseguire le operazioni seguenti:

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

2. Nel riquadro del menu in **Servizio BLOB** selezionare **Dominio personalizzato**.  
   Verrà visualizzato il riquadro **Dominio personalizzato**.

3. Cancellare il contenuto della casella di testo contenente il nome di dominio personalizzato.

4. Fare clic sul pulsante **Salva**.

Dopo che il dominio personalizzato è stato rimosso correttamente, verrà visualizzata una notifica del portale che indica che l'account di archiviazione è stato aggiornato correttamente

#### <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Per rimuovere la registrazione di un dominio personalizzato, usare il comando [az storage account update](https://docs.microsoft.com/cli/azure/storage/account) dell'interfaccia della riga di comando e quindi specificare una stringa vuota (`""`) per il valore dell'argomento `--custom-domain`.

* Formato del comando:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Esempio del comando:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

#### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per rimuovere la registrazione di un dominio personalizzato, usare il cmdlet di PowerShell [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) e quindi specificare una stringa vuota (`""`) per il valore dell'argomento `-CustomDomainName`.

* Formato del comando:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Esempio del comando:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```
---

<a id="enable-https" />

## <a name="map-a-custom-domain-with-https-enabled"></a>Eseguire il mapping di un dominio personalizzato con HTTPS abilitatoMap a custom domain with HTTPS enabled

Questo approccio prevede più passaggi, ma consente l'accesso HTTPS. 

Se non è necessario che gli utenti accedano al BLOB o al contenuto Web tramite HTTPS, vedere la sezione Eseguire il mapping di [un dominio personalizzato con solo HTTP abilitato](#enable-http) di questo articolo. 

Per eseguire il mapping di un dominio personalizzato e abilitare l'accesso HTTPS, eseguire le operazioni seguenti:To map a custom domain and enable HTTPS access, do the following:

1. Abilitare la rete CDN di Azure nel BLOB o nell'endpoint Web.Enable [Azure CDN](../../cdn/cdn-overview.md) on your blob or web endpoint. 

   Per un endpoint di archiviazione BLOB, vedere Integrare un account di archiviazione di Azure con la rete CDN di Azure.For a Blob Storage endpoint, see [Integrate an Azure storage account with Azure CDN.](../../cdn/cdn-create-a-storage-account-with-cdn.md) 

   Per un endpoint del sito Web statico, vedere Integrare un sito Web statico con la rete CDN di Azure.For a static website endpoint, see [Integrate a static website with Azure CDN.](static-website-content-delivery-network.md)

2. [Eseguire il mapping del contenuto della rete CDN a un dominio personalizzato](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Abilitare HTTPS in un dominio personalizzato della rete CDN di Azure.](../../cdn/cdn-custom-ssl.md)

   > [!NOTE] 
   > Quando si aggiorna il sito Web statico, assicurarsi di cancellare il contenuto memorizzato nella cache nei server perimetrali della rete CDN eliminando l'endpoint della rete CDN. Per altre informazioni, vedere [Ripulire un endpoint della rete CDN di Azure](../../cdn/cdn-purge-endpoint.md).

4. (Facoltativo) Rivedere le seguenti linee guida:

   * Token di firma di accesso condiviso con la rete CDN di [Azure.](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures)

   * [Reindirizzamento da HTTP a HTTPS con la rete CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection)di Azure.

   * Prezzi e fatturazione quando si usa Archiviazione BLOB con la [rete CDN di Azure.Pricing](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection)and billing when using Blob Storage with Azure CDN .

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull'hosting statico di siti Web nell'archiviazione BLOB di AzureLearn about static website hosting in Azure Blob storage](storage-blob-static-website.md)
