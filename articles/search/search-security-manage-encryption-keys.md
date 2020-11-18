---
title: Crittografia inattiva con chiavi gestite dal cliente
titleSuffix: Azure Cognitive Search
description: Integrare la crittografia lato server su indici e mappe sinonimi in Azure ricerca cognitiva usando chiavi create e gestite in Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 95ac4ed83a4486665ce378972ea7d6423c2482d5
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682911"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Configurare chiavi gestite dal cliente per la crittografia dei dati in Azure ricerca cognitiva

Azure ricerca cognitiva crittografa automaticamente il contenuto indicizzato inattivo con le [chiavi gestite dal servizio](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components). Se è necessaria una maggiore protezione, è possibile integrare la crittografia predefinita con un livello di crittografia aggiuntivo usando le chiavi create e gestite in Azure Key Vault. Questo articolo illustra i passaggi per la configurazione della crittografia CMK.

La crittografia CMK dipende dalla [Azure Key Vault](../key-vault/general/overview.md). È possibile creare chiavi di crittografia personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API di Azure Key Vault per generare chiavi di crittografia. Con Azure Key Vault, è anche possibile controllare l'utilizzo della chiave se si [Abilita la registrazione](../key-vault/general/logging.md).  

La crittografia con chiavi gestite dal cliente viene applicata a singoli indici o mappe sinonimi quando tali oggetti vengono creati e non è specificato nel livello di servizio di ricerca stesso. È possibile crittografare solo nuovi oggetti. Non è possibile crittografare il contenuto già esistente.

Non è necessario che tutte le chiavi si trovino nello stesso insieme di credenziali delle chiavi. Un singolo servizio di ricerca può ospitare più indici crittografati o mappe sinonime, ciascuno crittografato con le proprie chiavi di crittografia gestite dal cliente, archiviate in insiemi di credenziali delle chiavi diversi. È anche possibile avere indici e mappe sinonimi nello stesso servizio che non sono crittografati con chiavi gestite dal cliente.

>[!Important]
> Se si implementano chiavi gestite dal cliente, assicurarsi di seguire le procedure rigorose durante la rotazione di routine delle chiavi dell'insieme di credenziali delle chiavi e Active Directory la registrazione e i segreti dell'applicazione. Aggiornare sempre tutti i contenuti crittografati per usare nuovi segreti e chiavi prima di eliminare quelli precedenti. Se non si esegue questo passaggio, non sarà possibile decrittografare il contenuto.

## <a name="double-encryption"></a>Crittografia doppia

Per i servizi creati dopo il 1 ° agosto 2020 e in aree specifiche, l'ambito della crittografia CMK include dischi temporanei che raggiungono la [crittografia completa](search-security-overview.md#double-encryption), attualmente disponibile nelle aree geografiche seguenti: 

+ West US 2
+ Stati Uniti orientali
+ Stati Uniti centro-meridionali
+ US Gov Virginia
+ US Gov Arizona

Se si usa un'area diversa o un servizio creato prima del 1 ° agosto, la crittografia CMK è limitata solo al disco dati, esclusi i dischi temporanei utilizzati dal servizio.

## <a name="prerequisites"></a>Prerequisiti

In questo scenario vengono usati gli strumenti e i servizi seguenti.

+ [Azure ricerca cognitiva](search-create-service-portal.md) su un [livello fatturabile](search-sku-tier.md#tiers) (Basic o superiore, in qualsiasi area).
+ [Azure Key Vault](../key-vault/general/overview.md)è possibile creare un insieme di credenziali delle chiavi usando [portale di Azure](../key-vault//general/quick-create-portal.md), l'interfaccia della riga di comando di [Azure](../key-vault//general/quick-create-cli.md)o [Azure PowerShell](../key-vault//general/quick-create-powershell.md). nella stessa sottoscrizione di Azure ricerca cognitiva. Per l'insieme di credenziali delle chiavi deve essere abilitata la protezione **eliminazione** temporanea e **ripulitura** .
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Se non si dispone di un [nuovo tenant, configurare un nuovo tenant](../active-directory/develop/quickstart-create-new-tenant.md).

È necessario disporre di un'applicazione di ricerca in grado di creare l'oggetto crittografato. In questo codice si farà riferimento a una chiave dell'insieme di credenziali delle chiavi e Active Directory informazioni di registrazione. Questo codice può essere un'app funzionante o un codice del prototipo, ad esempio il codice [C# DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK).

> [!TIP]
> Per chiamare le API REST per la creazione di indici e mappe sinonimo che includono un parametro della chiave di crittografia, è possibile usare il [post](search-get-started-postman.md) o il [Azure PowerShell](./search-get-started-powershell.md) . Al momento non è disponibile il supporto del portale per l'aggiunta di una chiave a indici o mappe sinonimi.

## <a name="1---enable-key-recovery"></a>1-Abilita ripristino chiavi

A causa della natura della crittografia con chiavi gestite dal cliente, nessuno può recuperare i dati se la chiave dell'insieme di credenziali delle chiavi di Azure viene eliminata. Per evitare la perdita di dati causata da eliminazioni accidentali di chiavi di Key Vault, è necessario abilitare la protezione di eliminazione temporanea e ripulitura nell'insieme di credenziali delle chiavi. L'eliminazione temporanea è abilitata per impostazione predefinita, pertanto si verificheranno problemi solo se lo si disabilita intenzionalmente. Il ripulitura della protezione non è abilitata per impostazione predefinita, ma è necessaria per la crittografia CMK di Azure ricerca cognitiva. Per ulteriori informazioni, vedere la panoramica sulla protezione con [eliminazione](../key-vault/general/soft-delete-overview.md) temporanea e [ripulitura](../key-vault/general/soft-delete-overview.md#purge-protection) .

È possibile impostare entrambe le proprietà usando il portale, PowerShell o i comandi dell'interfaccia della riga di comando di Azure.

### <a name="using-azure-portal"></a>Uso del portale di Azure

1. [Accedere a portale di Azure](https://portal.azure.com) e aprire la pagina Panoramica di Key Vault.

1. Nella pagina **Overview** in **Essentials** abilitare l' **eliminazione** temporanea e l' **eliminazione della protezione**.

### <a name="using-powershell"></a>Utilizzo di PowerShell

1. Eseguire `Connect-AzAccount` per configurare le credenziali di Azure.

1. Eseguire il comando seguente per connettersi all'insieme di credenziali delle chiavi, sostituendo `<vault_name>` con un nome valido:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault viene creato con l'eliminazione temporanea abilitata. Se è disabilitato nell'insieme di credenziali, eseguire il comando seguente:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Abilita ripulitura protezione:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Salvare gli aggiornamenti:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

+ Se si dispone [di un'installazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), è possibile eseguire il comando seguente per abilitare le proprietà necessarie.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2-creare una chiave in Key Vault

Ignorare questo passaggio se si dispone già di una chiave in Azure Key Vault.

1. [Accedere a portale di Azure](https://portal.azure.com) e aprire la pagina Panoramica di Key Vault.

1. Selezionare **chiavi** a sinistra e quindi selezionare **+ genera/importa**.

1. Nell'elenco di **Opzioni** del riquadro **Crea chiave** scegliere il metodo che si desidera utilizzare per creare una chiave. È possibile **generare** una nuova chiave, **caricare** una chiave esistente o usare **Restore backup** per selezionare un backup di una chiave.

1. Immettere un **nome** per la chiave e, facoltativamente, selezionare altre proprietà chiave.

1. Selezionare **Crea** per avviare la distribuzione.

1. Prendere nota dell'identificatore di chiave, composto dall'URI del valore della **chiave**, dal nome della **chiave** e dalla versione della **chiave**. È necessario l'identificatore per definire un indice crittografato in ricerca cognitiva di Azure.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Creare una nuova chiave di Key Vault":::

## <a name="3---register-an-app-in-active-directory"></a>3-registrare un'app in Active Directory

1. In [portale di Azure](https://portal.azure.com)trovare la risorsa Azure Active Directory per la sottoscrizione.

1. A sinistra, in **Gestisci** selezionare **registrazioni app**, quindi selezionare **nuova registrazione**.

1. Assegnare un nome alla registrazione, ad esempio un nome simile al nome dell'applicazione di ricerca. Selezionare **Registra**.

1. Dopo la creazione della registrazione dell'app, copiare l'ID applicazione. Sarà necessario specificare questa stringa per l'applicazione. 

   Se si esegue l'istruzione/routine di [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK), incollare questo valore nel **appsettings.jssu** file.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="ID applicazione nella sezione Essentials":::

1. Selezionare quindi **certificati & segreti** a sinistra.

1. Selezionare **Nuovo segreto client**. Assegnare al segreto un nome visualizzato e selezionare **Aggiungi**.

1. Copiare il segreto dell'applicazione. Se si esegue l'istruzione nell'esempio, incollare questo valore nella **appsettings.jssu** file.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Segreto dell'applicazione":::

## <a name="4---grant-key-access-permissions"></a>4-concedere le autorizzazioni di accesso alla chiave

In questo passaggio verrà creato un criterio di accesso in Key Vault. Questo criterio assegna all'applicazione registrata con Active Directory autorizzazione per usare la chiave gestita dal cliente.

È possibile revocare le autorizzazioni di accesso in un determinato momento. Una volta revocato, qualsiasi indice del servizio di ricerca o mappa di sinonimi che usa tale Key Vault diventerà inutilizzabile. Il ripristino delle autorizzazioni di accesso di Key Vault in un secondo momento ripristinerà l'accesso alla mappa index\synonym. Per altre informazioni, vedere [proteggere l'accesso a un insieme di](../key-vault/general/secure-your-key-vault.md)credenziali delle chiavi.

1. Sempre nella portale di Azure aprire la pagina **Panoramica** di Key Vault. 

1. Selezionare i **criteri di accesso** a sinistra e selezionare **+ Aggiungi criteri di accesso**.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Aggiungere nuovi criteri di accesso di Key Vault":::

1. Scegliere **Seleziona entità** e selezionare l'applicazione registrata con Active Directory. È possibile cercarlo in base al nome.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Selezionare l'entità criteri di accesso dell'insieme di credenziali delle chiavi":::

1. In **autorizzazioni chiave** scegliere *Get*, *Unwrap Key* e *Wrap Key*.

1. In **autorizzazioni segrete** selezionare *Get*.

1. In **autorizzazioni certificato** selezionare *Get*.

1. Selezionare **Aggiungi** e quindi **Salva**.

> [!Important]
> Il contenuto crittografato in ricerca cognitiva di Azure è configurato per l'uso di una chiave di Azure Key Vault specifica con una **versione** specifica. Se si modifica la chiave o la versione, è necessario aggiornare la mappa degli indici o dei sinonimi per usare il nuovo key\version **prima** di eliminare il key\version. precedente In caso contrario, il rendering dell'indice o della mappa dei sinonimi sarà inutilizzabile e non sarà possibile decrittografare il contenuto quando l'accesso alla chiave viene perso.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5-crittografare il contenuto

Per aggiungere una chiave gestita dal cliente in un indice, un'origine dati, un skillt, un indicizzatore o una mappa di sinonimi, è necessario usare l' [API REST di ricerca](https://docs.microsoft.com/rest/api/searchservice/) o un SDK. Il portale non espone le mappe dei sinonimi o le proprietà di crittografia. Quando si usano gli indici API validi, le origini dati, skillsets, gli indicizzatori e le mappe sinonime supportano una proprietà **encryptionKey** di primo livello.

Questo esempio usa l'API REST con i valori per Azure Key Vault e Azure Active Directory:

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

> [!Note]
> Nessuno di questi dettagli dell'insieme di credenziali delle chiavi è considerato segreto e può essere facilmente recuperato passando alla pagina chiave Azure Key Vault pertinente in portale di Azure.

## <a name="example-index-encryption"></a>Esempio: crittografia degli indici

Creare un indice crittografato usando l' [API REST create index Azure ricerca cognitiva](https://docs.microsoft.com/rest/api/searchservice/create-index). Utilizzare la `encryptionKey` proprietà per specificare la chiave di crittografia da utilizzare.
> [!Note]
> Nessuno di questi dettagli dell'insieme di credenziali delle chiavi è considerato segreto e può essere facilmente recuperato passando alla pagina chiave Azure Key Vault pertinente in portale di Azure.

## <a name="rest-examples"></a>Esempi REST

Questa sezione illustra il codice JSON completo per un indice crittografato e una mappa di sinonimi

### <a name="index-encryption"></a>Crittografia dell'indice

Per informazioni dettagliate sulla creazione di un nuovo indice tramite l'API REST, vedere [create index (API REST)](/rest/api/searchservice/create-index), in cui l'unica differenza consiste nel specificare i dettagli della chiave di crittografia come parte della definizione dell'indice:

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

È ora possibile inviare la richiesta di creazione dell'indice, quindi iniziare a utilizzare l'indice normalmente.

### <a name="synonym-map-encryption"></a>Crittografia mappa sinonimi

Creare una mappa di sinonimi crittografata usando l' [API REST di creazione della mappa di Azure ricerca cognitiva](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map). Utilizzare la `encryptionKey` proprietà per specificare la chiave di crittografia da utilizzare.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

È ora possibile inviare la richiesta di creazione della mappa di sinonimi e iniziare a usarla normalmente.

## <a name="example-data-source-encryption"></a>Esempio: crittografia dell'origine dati

Creare un'origine dati crittografata usando l' [origine dati (API REST di Azure ricerca cognitiva)](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Utilizzare la `encryptionKey` proprietà per specificare la chiave di crittografia da utilizzare.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

È ora possibile inviare la richiesta di creazione dell'origine dati e iniziare a usarla normalmente.

## <a name="example-skillset-encryption"></a>Esempio: crittografia di competenze

Creare un oggetto con competenze crittografate usando l' [API REST di Azure ricerca cognitiva](https://docs.microsoft.com/rest/api/searchservice/create-skillset). Utilizzare la `encryptionKey` proprietà per specificare la chiave di crittografia da utilizzare.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

È ora possibile inviare la richiesta di creazione di competenze e iniziare a usarla normalmente.

## <a name="example-indexer-encryption"></a>Esempio: crittografia dell'indicizzatore

Creare un indicizzatore crittografato usando l' [API REST crea indicizzatore Azure ricerca cognitiva](https://docs.microsoft.com/rest/api/searchservice/create-indexer). Utilizzare la `encryptionKey` proprietà per specificare la chiave di crittografia da utilizzare.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

È ora possibile inviare la richiesta di creazione dell'indicizzatore e iniziare a usarla normalmente.

>[!Important]
> Sebbene `encryptionKey` non possa essere aggiunto agli indici di ricerca o ai mapping sinonimi esistenti, può essere aggiornato fornendo valori diversi per uno dei tre dettagli dell'insieme di credenziali delle chiavi (ad esempio, l'aggiornamento della versione della chiave). Quando si passa a una nuova chiave di Key Vault o a una nuova versione della chiave, qualsiasi indice di ricerca o mappa di sinonimi che usa la chiave deve prima essere aggiornato per usare il nuovo key\version **prima** di eliminare il key\version. precedente In caso contrario, il mapping dell'indice o del sinonimo sarà inutilizzabile, in quanto non sarà in grado di decrittografare il contenuto quando l'accesso alla chiave viene perso. Sebbene il ripristino delle autorizzazioni di accesso all'insieme di credenziali delle chiavi in un secondo momento ripristinerà l'accesso al contenuto

## <a name="simpler-alternative-trusted-service"></a>Alternativa più semplice: servizio attendibile

A seconda dei requisiti di autenticazione e configurazione del tenant, potrebbe essere possibile implementare un approccio più semplice per accedere a una chiave di Key Vault. Anziché creare e usare un'applicazione Active Directory, è possibile rendere un servizio di ricerca un servizio attendibile abilitando un'identità gestita dal sistema. Per accedere alla chiave dell'insieme di credenziali delle chiavi, si userà quindi il servizio di ricerca attendibile come principio di sicurezza, anziché come applicazione registrata AD.

Questo approccio consente di omettere i passaggi per la registrazione dell'applicazione e i segreti dell'applicazione e semplifica la definizione di una chiave di crittografia solo per i componenti dell'insieme di credenziali delle chiavi (URI, nome dell'insieme di credenziali, versione della chiave).

In generale, un'identità gestita consente al servizio di ricerca di eseguire l'autenticazione a Azure Key Vault senza archiviare le credenziali (ApplicationID o ApplicationSecret) nel codice. Il ciclo di vita di questo tipo di identità gestita è associato al ciclo di vita del servizio di ricerca, che può avere una sola identità gestita. Per altre informazioni sul funzionamento delle identità gestite, vedere [che cosa sono le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

1. Rendere il servizio di ricerca un servizio attendibile.
   ![Attivare l'identità gestita assegnata dal sistema](./media/search-managed-identities/turn-on-system-assigned-identity.png "Attivare l'identità gestita assegnata dal sistema")

1. Quando si configurano i criteri di accesso in Azure Key Vault, scegliere il servizio di ricerca attendibile come principio, anziché l'applicazione registrata AD. Assegnare le stesse autorizzazioni (multiple GETs, WRAP e unwrap) come indicato nel passaggio concedere le autorizzazioni per la chiave di accesso.

1. Utilizzare una costruzione semplificata dell'oggetto `encryptionKey` che omette le proprietà del Active Directory.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

Le condizioni che impediscono l'adozione di questo approccio semplificato includono:

+ Non è possibile concedere direttamente le autorizzazioni di accesso al servizio di ricerca all'insieme di credenziali delle chiavi, ad esempio se il servizio di ricerca si trova in un tenant di Active Directory diverso rispetto a quello Azure Key Vault.

+ È necessario un singolo servizio di ricerca per ospitare più mappe indexes\synonym crittografate, ognuna con una chiave diversa da un insieme di credenziali delle chiavi diverso, in cui ogni insieme di credenziali delle chiavi deve usare **un'identità diversa** per l'autenticazione. Poiché un servizio di ricerca può avere solo un'identità gestita, i requisiti per più identità hanno lo stesso approccio semplificato per lo scenario.  

## <a name="work-with-encrypted-content"></a>Usare il contenuto crittografato

Con la crittografia CMK si noterà la latenza per l'indicizzazione e le query a causa del lavoro di crittografia/decrittografia aggiuntivo. Azure ricerca cognitiva non registra l'attività di crittografia, ma è possibile monitorare l'accesso alle chiavi tramite la registrazione di Key Vault. Si consiglia di [abilitare la registrazione](../key-vault/general/logging.md) come parte della configurazione di Key Vault.

La rotazione della chiave dovrebbe verificarsi nel tempo. Ogni volta che si ruotano le chiavi, è importante seguire questa sequenza:

1. [Determinare la chiave utilizzata da una mappa di indici o sinonimi](search-security-get-encryption-keys.md).
1. [Creare una nuova chiave nell'insieme di](../key-vault/keys/quick-create-portal.md)credenziali delle chiavi, ma lasciare disponibile la chiave originale.
1. [Aggiornare le proprietà di encryptionKey](/rest/api/searchservice/update-index) in un indice o in una mappa di sinonimi per usare i nuovi valori. Solo gli oggetti creati originariamente con questa proprietà possono essere aggiornati in modo da usare un valore diverso.
1. Disabilitare o eliminare la chiave precedente nell'insieme di credenziali delle chiavi. Monitorare l'accesso alla chiave per verificare che la nuova chiave sia in uso.

Per motivi di prestazioni, il servizio di ricerca memorizza nella cache la chiave per un massimo di diverse ore. Se si disabilita o si elimina la chiave senza specificarne una nuova, le query continueranno a funzionare su base temporanea fino alla scadenza della cache. Tuttavia, quando il servizio di ricerca non è in grado di decrittografare il contenuto, viene ricevuto questo messaggio: "accesso non consentito. La chiave di query utilizzata potrebbe essere stata revocata. riprovare ". 

## <a name="next-steps"></a>Passaggi successivi

Se non si ha familiarità con l'architettura della sicurezza di Azure, vedere la [documentazione sulla sicurezza di Azure](../security/index.yml)e in particolare questo articolo:

> [!div class="nextstepaction"]
> [Crittografia dei dati inattivi](../security/fundamentals/encryption-atrest.md)
