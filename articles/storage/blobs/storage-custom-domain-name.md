---
title: Eseguire il mapping di un dominio personalizzato a un endpoint di archiviazione BLOB di Azure
titleSuffix: Azure Storage
description: Eseguire il mapping di un dominio personalizzato a un endpoint Web o di archiviazione BLOB in un account di archiviazione di Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 95fd62584ef73f3f2f198c84913652f460fc5b1a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84465542"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Eseguire il mapping di un dominio personalizzato a un endpoint di archiviazione BLOB di Azure

È possibile eseguire il mapping di un dominio personalizzato a un endpoint del servizio BLOB o a un endpoint del [sito Web statico](storage-blob-static-website.md) . 

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> Questo mapping funziona solo per i sottodomini (ad esempio, `www.contoso.com` ). Se si vuole che l'endpoint Web sia disponibile nel dominio radice (ad esempio `contoso.com` ,), è necessario usare la rete CDN di Azure. Per istruzioni, vedere la sezione [eseguire il mapping di un dominio personalizzato con HTTPS abilitato in](#enable-https) questo articolo. Poiché il passaggio a questa sezione di questo articolo per abilitare il dominio radice del dominio personalizzato, il passaggio all'interno di questa sezione per l'abilitazione di HTTPS è facoltativo. 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Eseguire il mapping di un dominio personalizzato con solo HTTP abilitato

Questo approccio è più semplice, ma consente solo l'accesso HTTP. Se l'account di archiviazione è configurato per [richiedere il trasferimento sicuro](../common/storage-require-secure-transfer.md) tramite HTTPS, è necessario abilitare l'accesso HTTPS per il dominio personalizzato. 

Per abilitare l'accesso HTTPS, vedere la sezione [eseguire il mapping di un dominio personalizzato con HTTPS abilitato](#enable-https) in questo articolo. 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>Eseguire il mapping di un dominio personalizzato

> [!IMPORTANT]
> Il dominio personalizzato sarà brevemente non disponibile per gli utenti durante il completamento della configurazione. Se il dominio attualmente supporta un'applicazione con un contratto di servizio (SLA) che richiede un tempo di inattività pari a zero, attenersi alla procedura descritta nella sezione [eseguire il mapping di un dominio personalizzato con zero tempo di inattività](#zero-down-time) di questo articolo per assicurarsi che gli utenti possano accedere al dominio durante l'operazione di mapping DNS.

Se non si desidera che il dominio sia temporaneamente non disponibile per gli utenti, attenersi alla seguente procedura.

: heavy_check_mark: passaggio 1: ottenere il nome host dell'endpoint di archiviazione.

: heavy_check_mark: passaggio 2: creare un record di nome canonico (CNAME) con il provider di dominio.

: heavy_check_mark: passaggio 3: registrare il dominio personalizzato con Azure. 

: heavy_check_mark: passaggio 4: testare il dominio personalizzato.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Passaggio 1: ottenere il nome host dell'endpoint di archiviazione 

Il nome host è l'URL dell'endpoint di archiviazione senza l'identificatore del protocollo e la barra finale. 

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

2. Nel riquadro dei menu, in **Impostazioni**, selezionare **Proprietà**.  

3. Copiare il valore dell'endpoint del **servizio BLOB primario** o dell' **endpoint del sito Web statico primario** in un file di testo. 

4. Rimuovere l'identificatore del protocollo (*ad esempio*, HTTPS) e la barra finale da tale stringa. La tabella seguente contiene esempi.

   | Tipo di endpoint |  endpoint | nome host |
   |------------|-----------------|-------------------|
   |servizio BLOB  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |sito Web statico  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Impostare questo valore per un momento successivo.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Passaggio 2: creare un record di nome canonico (CNAME) con il provider di dominio

Creare un record CNAME in modo che punti al nome host. Un record CNAME è un tipo di record DNS che esegue il mapping di un nome di dominio di origine a uno di destinazione.

1. Accedere al sito Web del registrar di dominio, quindi passare alla pagina per la gestione delle impostazioni DNS.

   che potrebbe essere contenuta in una sezione denominata **Nome di dominio**, **DNS** o **Gestione server dei nomi**.

2. Trovare la sezione per la gestione dei record CNAME. 

   Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME**, **Alias** o **Subdomains**.

3. Creare un record CNAME. Come parte di tale record, fornire gli elementi seguenti: 

   - Alias di sottodominio, ad esempio `www` o `photos` . Il sottodominio è obbligatorio. i domini radice non sono supportati. 
      
   - Il nome host ottenuto nella sezione [ottenere il nome host dell'endpoint di archiviazione](#endpoint) più indietro in questo articolo. 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Passaggio 3: registrare il dominio personalizzato con Azure

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

2. Nel riquadro del menu in **Servizio BLOB** selezionare **Dominio personalizzato**.  

   ![opzione del dominio personalizzato](./media/storage-custom-domain-name/custom-domain-button.png "dominio personalizzato")

   Verrà visualizzato il riquadro **Dominio personalizzato**.

3. Nella casella di testo **nome dominio** immettere il nome del dominio personalizzato, incluso il sottodominio  
   
   Se ad esempio il dominio è *contoso.com* e l'alias del sottodominio è *www*, immettere `www.contoso.com` . Se il sottodominio è *Photos*, immettere `photos.contoso.com` .

4. Per registrare il dominio personalizzato, scegliere il pulsante **Salva** .

   Dopo la propagazione del record CNAME attraverso i server dei nomi di dominio (DNS) e se gli utenti dispongono delle autorizzazioni appropriate, possono visualizzare i dati BLOB usando il dominio personalizzato.

#### <a name="step-4-test-your-custom-domain"></a>Passaggio 4: testare il dominio personalizzato

Per verificare che il mapping del dominio personalizzato all'endpoint del servizio BLOB sia stato eseguito, creare un BLOB in un contenitore pubblico all'interno dell'account di archiviazione. Quindi, in un Web browser, accedere al BLOB usando un URI nel formato seguente: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Per accedere a un modulo Web nel contenitore *myforms* nel sottodominio personalizzato *photos.contoso.com*, è possibile ad esempio usare l'URI seguente: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>Eseguire il mapping di un dominio personalizzato senza tempi di inattività

> [!NOTE]
> Per evitare che il dominio sia temporaneamente non disponibile per gli utenti, è consigliabile seguire la procedura descritta nella sezione [eseguire il mapping di un dominio personalizzato](#map-a-domain) di questo articolo. Si tratta di un approccio più semplice con un minor numero di passaggi.  

Se il dominio attualmente supporta un'applicazione con un contratto di servizio (SLA) che richiede un tempo di inattività pari a zero, attenersi alla procedura seguente per assicurarsi che gli utenti possano accedere al dominio durante l'operazione di mapping DNS. 

: heavy_check_mark: passaggio 1: ottenere il nome host dell'endpoint di archiviazione.

: heavy_check_mark: passaggio 2: creare un record di nome canonico intermediario (CNAME) con il provider di dominio.

: heavy_check_mark: passaggio 3: pre-registrare il dominio personalizzato con Azure.

: heavy_check_mark: passaggio 4: creare un record CNAME con il provider di dominio.

: heavy_check_mark: passaggio 5: testare il dominio personalizzato.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Passaggio 1: ottenere il nome host dell'endpoint di archiviazione 

Il nome host è l'URL dell'endpoint di archiviazione senza l'identificatore del protocollo e la barra finale. 

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

2. Nel riquadro dei menu, in **Impostazioni**, selezionare **Proprietà**.  

3. Copiare il valore dell'endpoint del **servizio BLOB primario** o dell' **endpoint del sito Web statico primario** in un file di testo. 

4. Rimuovere l'identificatore del protocollo (*ad esempio*, HTTPS) e la barra finale da tale stringa. La tabella seguente contiene esempi.

   | Tipo di endpoint |  endpoint | nome host |
   |------------|-----------------|-------------------|
   |servizio BLOB  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |sito Web statico  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Impostare questo valore per un momento successivo.

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Passaggio 2: creare un record di nome canonico intermediario (CNAME) con il provider di dominio

Creare un record CNAME temporaneo per puntare al nome host. Un record CNAME è un tipo di record DNS che esegue il mapping di un nome di dominio di origine a uno di destinazione.

1. Accedere al sito Web del registrar di dominio, quindi passare alla pagina per la gestione delle impostazioni DNS.

   che potrebbe essere contenuta in una sezione denominata **Nome di dominio**, **DNS** o **Gestione server dei nomi**.

2. Trovare la sezione per la gestione dei record CNAME. 

   Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME**, **Alias** o **Subdomains**.

3. Creare un record CNAME. Come parte di tale record, fornire gli elementi seguenti: 

   - Alias di sottodominio, ad esempio `www` o `photos` . Il sottodominio è obbligatorio. i domini radice non sono supportati.

     Aggiungere il `asverify` sottodominio all'alias. Ad esempio, `asverify.www` o `asverify.photos`.
       
   - Il nome host ottenuto nella sezione [ottenere il nome host dell'endpoint di archiviazione](#endpoint) più indietro in questo articolo. 

     Aggiungere il sottodominio `asverify` al nome host. Ad esempio: `asverify.mystorageaccount.blob.core.windows.net`.

4. Per registrare il dominio personalizzato, scegliere il pulsante **Salva** .

   Se la registrazione ha esito positivo, una notifica nel portale informa che l'account di archiviazione è stato aggiornato correttamente. Il dominio personalizzato è stato verificato da Azure, ma il traffico verso il dominio non è ancora instradato all'account di archiviazione.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Passaggio 3: pre-registrare il dominio personalizzato con Azure

Quando si esegue la pre-registrazione del dominio personalizzato con Azure, si consente ad Azure di riconoscere il dominio personalizzato senza dover modificare il record DNS per il dominio. In questo modo, quando si modifica il record DNS per il dominio, verrà eseguito il mapping all'endpoint BLOB senza tempi di inattività.

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

2. Nel riquadro del menu in **Servizio BLOB** selezionare **Dominio personalizzato**.  

   ![opzione del dominio personalizzato](./media/storage-custom-domain-name/custom-domain-button.png "dominio personalizzato")

   Verrà visualizzato il riquadro **Dominio personalizzato**.

3. Nella casella di testo **nome dominio** immettere il nome del dominio personalizzato, incluso il sottodominio  
   
   Se ad esempio il dominio è *contoso.com* e l'alias del sottodominio è *www*, immettere `www.contoso.com` . Se il sottodominio è *Photos*, immettere `photos.contoso.com` .

4. Selezionare la casella di controllo **Usa convalida CNAME indiretta**.

5. Per registrare il dominio personalizzato, scegliere il pulsante **Salva** .
  
   Dopo la propagazione del record CNAME attraverso i server dei nomi di dominio (DNS) e se gli utenti dispongono delle autorizzazioni appropriate, possono visualizzare i dati BLOB usando il dominio personalizzato.

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Passaggio 4: creare un record CNAME con il provider di dominio

Creare un record CNAME temporaneo per puntare al nome host.

1. Accedere al sito Web del registrar di dominio, quindi passare alla pagina per la gestione delle impostazioni DNS.

   che potrebbe essere contenuta in una sezione denominata **Nome di dominio**, **DNS** o **Gestione server dei nomi**.

2. Trovare la sezione per la gestione dei record CNAME. 

   Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME**, **Alias** o **Subdomains**.

3. Creare un record CNAME. Come parte di tale record, fornire gli elementi seguenti: 

   - Alias di sottodominio, ad esempio `www` o `photos` . Il sottodominio è obbligatorio. i domini radice non sono supportati.
      
   - Il nome host ottenuto nella sezione [ottenere il nome host dell'endpoint di archiviazione](#endpoint-2) più indietro in questo articolo. 

#### <a name="step-5-test-your-custom-domain"></a>Passaggio 5: testare il dominio personalizzato

Per verificare che il mapping del dominio personalizzato all'endpoint del servizio BLOB sia stato eseguito, creare un BLOB in un contenitore pubblico all'interno dell'account di archiviazione. Quindi, in un Web browser, accedere al BLOB usando un URI nel formato seguente: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Per accedere a un modulo Web nel contenitore *myforms* nel sottodominio personalizzato *photos.contoso.com*, è possibile ad esempio usare l'URI seguente: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Rimuovere un mapping del dominio personalizzato

Per rimuovere un mapping di dominio personalizzato, annullare la registrazione del dominio personalizzato. Utilizzare una delle seguenti procedure.

#### <a name="portal"></a>[Portale](#tab/azure-portal)

Per rimuovere l'impostazione di dominio personalizzato, eseguire le operazioni seguenti:

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

2. Nel riquadro del menu in **Servizio BLOB** selezionare **Dominio personalizzato**.  
   Verrà visualizzato il riquadro **Dominio personalizzato**.

3. Cancellare il contenuto della casella di testo contenente il nome di dominio personalizzato.

4. Fare clic sul pulsante **Salva**.

Dopo la rimozione del dominio personalizzato, viene visualizzata una notifica del portale che l'account di archiviazione è stato aggiornato correttamente

#### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

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

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>Eseguire il mapping di un dominio personalizzato con HTTPS abilitato

Questo approccio prevede più passaggi, ma consente l'accesso HTTPS. 

Se non è necessario che gli utenti accedano al contenuto BLOB o Web usando HTTPS, vedere la sezione [eseguire il mapping di un dominio personalizzato con solo http abilitato](#enable-http) in questo articolo. 

Per eseguire il mapping di un dominio personalizzato e abilitare l'accesso HTTPS, procedere come segue:

1. Abilitare la rete [CDN di Azure](../../cdn/cdn-overview.md) nell'endpoint Web o BLOB. 

   Per un endpoint di archiviazione BLOB, vedere [integrare un account di archiviazione di Azure con la rete CDN di Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md). 

   Per un endpoint del sito Web statico, vedere [integrare un sito Web statico con la rete CDN di Azure](static-website-content-delivery-network.md).

2. [Eseguire il mapping del contenuto della rete CDN a un dominio personalizzato](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Abilitare HTTPS in un dominio personalizzato della rete CDN di Azure](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > Quando si aggiorna il sito Web statico, assicurarsi di cancellare il contenuto memorizzato nella cache nei server perimetrali della rete CDN ripulendo l'endpoint della rete CDN. Per altre informazioni, vedere [Ripulire un endpoint della rete CDN di Azure](../../cdn/cdn-purge-endpoint.md).

4. Opzionale Esaminare le linee guida seguenti:

   * [Token di firma di accesso condiviso (SAS) con la rete CDN di Azure](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures).

   * [Reindirizzamento da http a HTTPS con la rete CDN di Azure](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

   * [Prezzi e fatturazione quando si usa l'archiviazione BLOB con la rete CDN di Azure](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull'hosting di siti web statici nell'archivio BLOB di Azure](storage-blob-static-website.md)
