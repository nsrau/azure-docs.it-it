---
title: Caricare file in un account di Servizi multimediali mediante REST | Microsoft Docs
description: Informazioni su come ottenere contenuti multimediali in servizi multimediali tramite la creazione e il caricamento di asset con REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 49863bec4cbd367b6b309ef5a79e7287cb53ee5b
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042968"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Caricare file in un account di Servizi multimediali mediante REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portale](media-services-portal-upload-files.md)
> 

In Servizi multimediali è possibile caricare i file digitali in un asset. L'entità [Asset](/rest/api/media/operations/asset) può contenere video, audio, immagini, raccolte di anteprime, tracce di testo e file di sottotitoli codificati, oltre ai metadati relativi a questi file.  Dopo il caricamento dei file nell'asset, i contenuti vengono archiviati in modo sicuro nel cloud per altre operazioni di elaborazione e streaming. 

Questa esercitazione illustra come caricare un file ed eseguire altre operazioni associate:

> [!div class="checklist"]
> * Configurare Postman per tutte le operazioni di caricamento
> * Connettersi a Servizi multimediali 
> * Creare un criterio di accesso con autorizzazione di scrittura
> * Creare un asset
> * Creare un localizzatore di firma di accesso condiviso e creare l'URL di caricamento
> * Caricare un file nell'archiviazione BLOB usando l'URL di caricamento
> * Creare nell'asset i metadati per il file multimediale caricato

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- [Creare un account Servizi multimediali di Azure con il portale di Azure](media-services-portal-create-account.md).
- Rivedere l'argomento di carattere generale [Accesso all'API di Servizi multimediali di Azure con l'autenticazione di Azure AD](media-services-use-aad-auth-to-access-ams-api.md).
- Per altre informazioni, vedere anche l'articolo [usare l'autenticazione Azure ad per accedere all'API servizi multimediali con REST](./media-services-rest-connect-with-aad.md) .
- Configurare **Postman** come descritto in [Configurare Postman per le chiamate API REST di Servizi multimediali](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Considerazioni

Quando si usa l'API REST di Servizi multimediali, tenere presenti le seguenti considerazioni:
 
* Quando si accede alle entità con l'API REST di Servizi multimediali, è necessario impostare valori e campi di intestazione specifici nelle richieste HTTP. Per altre informazioni, vedere [Panoramica dell'API REST di Servizi multimediali](media-services-rest-how-to-use.md). <br/>Con la raccolta Postman usata in questa esercitazione vengono impostate tutte le intestazioni necessarie.
* Servizi multimediali usa il valore della proprietà IAssetFile.Name durante la creazione di URL per il contenuto di streaming, ad esempio http://{AMSAccount}. Origin. MediaServices. Windows. NET/{GUID}/{IAssetFile. Name}/streamingParameters. Per questo motivo, la codifica percentuale non è consentita. Il valore della proprietà **Name** non può contenere i [caratteri riservati per la codifica percentuale](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) seguenti: !*'();:@&=+$,/?%#[]". L'estensione del nome di file, inoltre, può essere preceduta da un solo punto (.).
* La lunghezza del nome non deve essere superare i 260 caratteri.
* È previsto un limite per le dimensioni massime dei file supportate per l'elaborazione in Servizi multimediali. Vedere [questo](media-services-quotas-and-limitations.md) articolo per informazioni dettagliate sulla limitazione per le dimensioni dei file.

## <a name="set-up-postman"></a>Configurare Postman

Per la procedura di configurazione di Postman per questa esercitazione, vedere [Configurare Postman per le chiamate API REST di Servizi multimediali](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Connettersi a Servizi multimediali

1. Aggiungere i valori della connessione all'ambiente. 

    È necessario impostare manualmente alcune variabili che fanno parte dell' [ambiente](postman-environment.md) **MediaServices** prima di poter iniziare a eseguire le operazioni definite nella [raccolta](postman-collection.md).

    Per ottenere i valori per le prime cinque variabili, vedere [Accesso all'API di Servizi multimediali di Azure con l'autenticazione di Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Screenshot che mostra l'icona "COG" selezionata in alto a destra e le prime cinque variabili selezionate dalla scheda "ambienti di gestione".](./media/media-services-rest-upload-files/postman-import-env.png)
2. Specificare il valore per la variabile di ambiente **MediaFileName** .

    Specificare il nome file del file multimediale che si intende caricare. In questo esempio verrà usato il file BigBuckBunny.mp4. 
3. Esaminare il file **AzureMediaServices.postman_environment.json** . Si noterà che quasi tutte le operazioni nella raccolta eseguono uno script "test". Gli script accettano alcuni valori restituito dalla risposta e impostano le variabili di ambiente appropriate.

    Ad esempio, la prima operazione ottiene un token di accesso e lo imposta nella variabile di ambiente **AccessToken** che viene usata in tutte le altre operazioni.

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. A sinistra della finestra di **posting** fare clic su **1. Ottenere il token di autenticazione AAD**  ->  **Get Azure ad token per l'entità servizio** .

    La parte URL viene compilata con la variabile di ambiente **AzureADSTSEndpoint** (precedentemente nell'esercitazione si impostano i valori delle variabili di ambiente che supportano la raccolta).

    ![Screenshot che mostra "1. Ottenere un token di autenticazione A D: ottenere un token di Azure A D per l'entità servizio "selezionato dalla finestra" post "e il pulsante" Invia "selezionato.](./media/media-services-rest-upload-files/postment-get-token.png)

5. Fare clic su **Invia** .

    Si noterà che la risposta contiene "access_token". Lo script "test" accetta questo valore e imposta la variabile di ambiente **AccessToken** (come descritto in precedenza). Se si esaminano le variabili di ambiente, si noterà che questa variabile contiene ora il valore del token di accesso (bearer token) usato nel resto delle operazioni. 

    Se il token scade, ripetere il passaggio "Get Azure AD Token for Service Principal". 

## <a name="create-an-access-policy-with-write-permission"></a>Creare un criterio di accesso con autorizzazione di scrittura

### <a name="overview"></a>Panoramica 

>[!NOTE]
>È previsto un limite di 1.000.000 di criteri per i diversi criteri AMS (ad esempio per i criteri Locator o ContentKeyAuthorizationPolicy). Usare lo stesso ID criterio se si usano sempre gli stessi giorni/autorizzazioni di accesso, come nel cado di criteri per i localizzatori che devono rimanere attivi per molto tempo (criteri di non caricamento). Per altre informazioni, vedere [questo](media-services-dotnet-manage-entities.md#limit-access-policies) articolo.

Prima di caricare i file nell'archiviazione BLOB, impostare i diritti dei criteri di accesso per la scrittura in un asset. A questo scopo, inviare una richiesta HTTP al set di entità AccessPolicies. Definire un valore DurationInMinutes durante la creazione. In caso contrario, si riceverà il messaggio di errore interno 500 del server in risposta. Per altre informazioni su AccessPolicies, vedere [AccessPolicy](/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Creare i criteri di accesso

1. Selezionare **AccessPolicy**  ->  **Crea AccessPolicy per il caricamento** .
2. Fare clic su **Invia** .

    ![Screenshot che mostra "AccessPolicy-crea AccessPolicy per il caricamento" selezionato dal menu a sinistra e il pulsante "Invia" selezionato.](./media/media-services-rest-upload-files/postman-access-policy.png)

    Lo script "test" ottiene l'ID AccessPolicy e imposta la variabile di ambiente appropriata.

## <a name="create-an-asset"></a>Creare un asset

### <a name="overview"></a>Panoramica

Un [Asset](/rest/api/media/operations/asset) è un contenitore di più tipi o set di oggetti in servizi multimediali, tra cui video, audio, immagini, raccolte di anteprime, tracce di testo e file di sottotitoli chiusi. Nell'API REST, la creazione di un asset richiede l'invio di una richiesta POST a Servizi multimediali e l'inserimento di tutte le informazioni sulle proprietà relative all'asset nel corpo della richiesta.

Una delle proprietà che è possibile aggiungere quando si crea un asset è **Options** . È possibile specificare una delle opzioni di crittografia seguenti: **None** (impostazione predefinita, non viene usata alcuna crittografia), **StorageEncrypted** (per contenuto che è stato pre-crittografato con crittografia di archiviazione lato client), **CommonEncryptionProtected** o **EnvelopeEncryptionProtected** . Se è presente un asset crittografato, è necessario configurare un criterio di recapito. Per altre informazioni, vedere [configurazione dei criteri di distribuzione degli asset](media-services-rest-configure-asset-delivery-policy.md).

Se l'asset è crittografato, è necessario creare un'entità **ContentKey** e collegarla all'asset, come descritto nell'articolo [Creazione di entità ContentKey mediante REST](media-services-rest-create-contentkey.md). Dopo il caricamento dei file nell'asset è necessario aggiornare le proprietà di crittografia nell'entità **AssetFile** con i valori ottenuti durante la crittografia dell'entità **Asset** . Effettuare questa operazione usando la richiesta HTTP **MERGE** . 

In questo esempio viene creato un asset non crittografato. 

### <a name="create-an-asset"></a>Creare un asset

1. Selezionare **Asset**  ->  **Crea asset** .
2. Fare clic su **Invia** .

    ![Screenshot che mostra "asset-crea asset" selezionato dal menu "raccolte" e il pulsante "Invia" selezionato.](./media/media-services-rest-upload-files/postman-create-asset.png)

    Lo script "test" ottiene l'ID Asset e imposta la variabile di ambiente appropriata.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Creare un localizzatore di firma di accesso condiviso e creare l'URL di caricamento

### <a name="overview"></a>Panoramica

Una volta impostati AccessPolicy e Locator, il file effettivo viene caricato nel contenitore di archiviazione BLOB di Azure usando le API REST di Archiviazione di Azure. È necessario caricare i file come BLOB in blocchi. I BLOB di pagine non sono supportati da Servizi multimediali di Azure.  

Per altre informazioni sull'uso dei BLOB di Archiviazione di Azure, vedere [API REST del servizio BLOB](/rest/api/storageservices/blob-service-rest-api).

Per ricevere l'URL di caricamento effettivo, creare un localizzatore di firma di accesso condiviso (illustrato di seguito). I localizzatori definiscono l'ora di inizio e il tipo di endpoint della connessione per i client che richiedono l'accesso ai file in un asset. È possibile creare più entità Locator per una specifica coppia AccessPolicy e Asset in modo da gestire le diverse richieste ed esigenze dei client. Ogni localizzatore usa i valori StartTime e DurationInMinutes di AccessPolicy per determinare la durata d'uso di un URL. Per altre informazioni, vedere [Locator](/rest/api/media/operations/locator).

Un URL di firma di accesso condiviso ha il seguente formato:

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

### <a name="considerations"></a>Considerazioni

Considerazioni applicabili:

* Non è possibile avere più di cinque localizzatori univoci associati contemporaneamente a un determinato asset. Per altre informazioni, vedere Locator.
* Se è necessario caricare i file immediatamente, impostare il valore StartTime su cinque minuti prima dell'ora corrente. Potrebbe infatti essere presente una leggera differenza di orario tra il computer client e Servizi multimediali. Inoltre, il valore StartTime deve essere nel formato data/ora seguente: AAAA-MM-GGTHH:mm:ss (ad esempio, "2014-05-23T17:53:50Z").    
* Può verificarsi un ritardo di 30-40 secondi tra la creazione di un localizzatore e la relativa disponibilità per l'uso.

### <a name="create-a-sas-locator"></a>Creare un localizzatore di firma di accesso condiviso

1. Selezionare **Locator**  ->  **create SAS Locator** .
2. Fare clic su **Invia** .

    Lo script "test" crea l'URl di caricamento sulla base del nome del file multimediale specificato e delle informazioni del localizzatore di firma di accesso condiviso e imposta la variabile di ambiente appropriata.

    ![Screenshot che mostra "Locator-Create A S Locator" selezionato dal menu "Collections" e il pulsante "Send" selezionato.](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Caricare un file nell'archiviazione BLOB usando l'URL di caricamento

### <a name="overview"></a>Panoramica

Dopo aver creato l'URL di caricamento, è necessario scrivere codice usando direttamente le API di BLOB di Azure per caricare il file nel contenitore della firma di accesso condiviso. Per altre informazioni, vedere gli articoli seguenti:

- [Uso dell'API REST di Archiviazione di Azure](../../storage/common/storage-rest-api-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Inserisci BLOB](/rest/api/storageservices/put-blob)
- [Caricare BLOB in Archiviazione BLOB](/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Caricare un file con Postman

A titolo di esempio verrà usato Postman per caricare un file MP4 di piccole dimensioni. Il caricamento di file binari tramite Postman potrebbe essere soggetto a limitazioni relative alle dimensioni file.

La richiesta di caricamento non fa parte della raccolta **AzureMedia** . 

Creare e configurare una nuova richiesta:
1. Premere **+** per creare una nuova scheda di richiesta.
2. Selezionare l'operazione **PUT** e incollare **{{UploadURL}}** nell'URL.
2. Lasciare invariata la scheda **Autorizzazione** (non impostarla su **Bearer Token** ).
3. Nella scheda **Intestazioni** specificare "x-ms-blob-type" in **Chiave** e "BlockBlob" in **Valore** .
2. Nella scheda **Corpo** fare clic su **binario** .
4. Scegliere il file con il nome specificato nella variabile di ambiente **MediaFileName** .
5. Fare clic su **Invia** .

    ![Screenshot che mostra la scheda "(carica R L)" selezionata.](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Creare i metadati nell'asset

Dopo aver caricato il file è necessario creare nell'asset i metadati per il file multimediale caricato nella risorsa di archiviazione BLOB associata all'asset.

1. Selezionare **AssetFiles**  ->  **CreateFileInfos** .
2. Fare clic su **Invia** .

    ![Caricare un file](./media/media-services-rest-upload-files/postman-create-file-info.png)

È necessario che il file sia caricato e che i relativi metadati siano impostati.

## <a name="validate"></a>Convalida

Per convalidare il corretto caricamento del file, è possibile eseguire una query su [AssetFile](/rest/api/media/operations/assetfile) e confrontare il valore di **ContentFileSize** (o altri dettagli) con quanto dovrebbe essere presente nel nuovo asset. 

Ad esempio, l'operazione **GET** consente di ottenere i dati relativi al file di asset, in questo caso il file BigBuckBunny.mp4. Per la query vengono usate le [variabili di ambiente](postman-environment.md) impostate in precedenza.

`{{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files`

La risposta conterrà dimensioni, nome e altre informazioni.

```console
"Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
"Name": "BigBuckBunny.mp4",
"ContentFileSize": "3186542",
"ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
```
  
## <a name="next-steps"></a>Passaggi successivi

Ora è possibile codificare gli asset caricati. Per altre informazioni, vedere [Encode assets](media-services-portal-encode.md)(Codificare gli asset).

È anche possibile usare Funzioni di Azure per attivare un processo di codifica basato su un file che arriva nel contenitore configurato. Per altre informazioni, vedere [questo esempio](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).
