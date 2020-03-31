---
title: Crittografia inattivi con chiavi gestite dal clienteEncryption-at-rest using customer-managed keys
titleSuffix: Azure Cognitive Search
description: Integrare la crittografia lato server sugli indici e sulle mappe dei sinonimi in Ricerca cognitiva di Azure usando le chiavi create e gestite in Archiviazione chiavi di Azure.En server-side encryption over indexes and synonym maps in Azure Cognitive Search using keys you create and manage in Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: cb17fe24339ad618229b3456ece15c206f79bdb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76899937"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Crittografia in base al rest del contenuto in Ricerca cognitiva di Azure usando chiavi gestite dal cliente in Archiviazione chiavi di AzureEncryption-at-rest of content in Azure Cognitive Search using customer-managed keys in Azure Key Vault

Per impostazione predefinita, Ricerca cognitiva di Azure crittografa il contenuto indicizzato inattivi con [chiavi gestite dal servizio.](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models) You can supplement default encryption with an additional encryption layer using keys that you create and manage in Azure Key Vault. In questo articolo vengono illustrati i passaggi.

La crittografia lato server è supportata tramite l'integrazione con [l'insieme di credenziali delle chiavi](https://docs.microsoft.com/azure/key-vault/key-vault-overview)di Azure. È possibile creare chiavi di crittografia personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API di Azure Key Vault per generare chiavi di crittografia. Con l'insieme di credenziali delle chiavi di Azure è anche possibile controllare l'utilizzo delle chiavi. 

La crittografia con chiavi gestite dal cliente viene configurata a livello di mappa di indice o sinonimo quando tali oggetti vengono creati e non a livello di servizio di ricerca. Non è possibile crittografare il contenuto già esistente. 

Le chiavi non devono essere tutte nello stesso insieme di credenziali delle chiavi. Un singolo servizio di ricerca può ospitare più indici crittografati o mappe di sinonimi, ognuno crittografato con le proprie chiavi di crittografia gestite dal cliente archiviate in archivi chiavi diversi.  È inoltre possibile avere indici e mappe dei sinonimi nello stesso servizio che non sono crittografati utilizzando chiavi gestite dal cliente. 

> [!IMPORTANT] 
> Questa funzionalità è disponibile nella [versione dell'API REST 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) e [in .NET SDK versione 8.0-preview.](search-dotnet-sdk-migration-version-9.md) Attualmente non è disponibile alcun supporto per configurare le chiavi di crittografia gestita del cliente nel portale di Azure.There is currently no support to configure customer managed encryption keys in the Azure portal. Il servizio di ricerca deve essere creato dopo gennaio 2019 e non può essere un servizio gratuito (condiviso).

## <a name="prerequisites"></a>Prerequisiti

In questo esempio vengono utilizzati i servizi seguenti. 

+ [Creare un servizio di Ricerca cognitiva di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. 

+ Creare una [risorsa dell'insieme di](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) credenziali delle chiavi di Azure o trovare un insieme di credenziali esistente nella sottoscrizione.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) o [l'interfaccia della](https://docs.microsoft.com/cli/azure/install-azure-cli) riga di comando di Azure viene usata per le attività di configurazione.

+ [Postman](search-get-started-postman.md), [Azure PowerShell](search-create-index-rest-api.md) e [Azure Cognitive Search SDK](https://aka.ms/search-sdk-preview) possono essere usati per chiamare l'API REST. Al momento non è disponibile alcun supporto del portale per la crittografia gestita dal cliente.

>[!Note]
> A causa della natura della crittografia con la funzionalità delle chiavi gestite dal cliente, Ricerca cognitiva di Azure non sarà in grado di recuperare i dati se la chiave dell'insieme di credenziali delle chiavi di Azure viene eliminata. Per evitare la perdita di dati causata da eliminazioni accidentali delle chiavi dell'insieme di credenziali delle chiavi, è **necessario** abilitare la protezione di eliminazione temporanea ed eliminazione nell'insieme di credenziali delle chiavi prima che possa essere utilizzata. Per altre informazioni, vedere Eliminazione temporanea di Archiviazione chiave di Azure.For more information, see [Azure Key Vault soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="1---enable-key-recovery"></a>1 - Abilitare il ripristino dei tasti

Dopo aver creato la risorsa Archivio chiavi di Azure, abilitare La protezione di eliminazione ed eliminazione temporanea nell'insieme di credenziali delle chiavi selezionato eseguendo i comandi di PowerShell o dell'interfaccia della riga di comando di Azure seguenti:After creating the Azure Key Vault resource, enable **Soft Purge** and **Purge Protection** in the selected Key vault by executing the following PowerShell or Azure CLI commands:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 - Creare una nuova chiave

Se si usa una chiave esistente per crittografare il contenuto di Ricerca cognitiva di Azure, ignorare questo passaggio.

1. [Accedere al portale](https://portal.azure.com) di Azure e passare al dashboard dell'insieme di credenziali delle chiavi.

1. Selezionare l'impostazione **Tasti** nel riquadro di spostamento a sinistra, quindi fare clic su **Genera/Importa**.

1. Nell'elenco **Opzioni**del riquadro **Crea una chiave** scegliere il metodo che si desidera utilizzare per creare una chiave. È possibile **generare** una nuova chiave, **caricare** una chiave esistente o utilizzare **Ripristina backup** per selezionare un backup di una chiave.

1. Immettere un **Nome** per la chiave e, facoltativamente, selezionare altre proprietà della chiave.

1. Fare clic sul pulsante **Crea** per avviare la distribuzione.

Prendere nota dell'identificatore di chiave, composto dal valore della **chiave Uri**, dal nome della **chiave**e dalla versione della **chiave**. Queste saranno necessarie per definire un indice crittografato in Ricerca cognitiva di Azure.You will need these to define an encrypted index in Azure Cognitive Search.
 
![Creare una nuova chiave dell'insieme di credenziali delle chiaviCreate a new key vault key key key key](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Creare una nuova chiave dell'insieme di credenziali delle chiaviCreate a new key vault key key key key")

## <a name="3---create-a-service-identity"></a>3 - Creare un'identità del servizio

L'assegnazione di un'identità al servizio di ricerca consente di concedere le autorizzazioni di accesso a Key Vault al servizio di ricerca. Il servizio di ricerca utilizzerà la propria identità per l'autenticazione con l'insieme di credenziali delle chiavi di Azure.Your search service will use its identity to authenticate with Azure Key vault.

Ricerca cognitiva di Azure supporta due modi per l'assegnazione dell'identità: un'identità gestita o un'applicazione Azure Active Directory gestita esternamente. 

Se possibile, utilizzare un'identità gestita. È il modo più semplice per assegnare un'identità al servizio di ricerca e dovrebbe funzionare nella maggior parte degli scenari. Se si usano più chiavi per indici e mappe dei sinonimi o se la soluzione si trova in un'architettura distribuita che disattiva l'autenticazione basata sull'identità, usare l'approccio avanzato di [Azure Active Directory gestito esternamente](#aad-app) descritto alla fine di questo articolo.

 In generale, un'identità gestita consente al servizio di ricerca di eseguire l'autenticazione in Un insieme di credenziali delle chiavi di Azure senza archiviare le credenziali nel codice. Il ciclo di vita di questo tipo di identità gestita è legato al ciclo di vita del servizio di ricerca, che può avere una sola identità gestita. [Ulteriori informazioni sulle identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Per creare un'identità gestita, [accedere al portale](https://portal.azure.com) di Azure e aprire il dashboard del servizio di ricerca. 

1. Fare clic su **Identità** nel riquadro di spostamento sinistro, modificarne lo stato in **Attivato**e fare clic su **Salva.**

![Abilitare un'identità gestitaEnable a managed identity](./media/search-enable-msi/enable-identity-portal.png "Abilitare un'identità manofila")

## <a name="4---grant-key-access-permissions"></a>4 - Concedere le autorizzazioni di accesso chiave

Per consentire al servizio di ricerca di utilizzare la chiave dell'insieme di credenziali delle chiavi, è necessario concedere al servizio di ricerca determinate autorizzazioni di accesso.

Le autorizzazioni di accesso possono essere revocate in qualsiasi momento. Una volta revocato, qualsiasi indice del servizio di ricerca o mappa dei sinonimi che utilizza tale insieme di credenziali delle chiavi diventerà inutilizzabile. Il ripristino delle autorizzazioni di accesso all'insieme di credenziali delle chiavi in un secondo momento ripristinerà l'accesso alla mappa dell'indice: sinonimo. Per ulteriori informazioni, consultate [Proteggere l'accesso a un insieme di credenziali](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)delle chiavi.

1. [Accedere al portale](https://portal.azure.com) di Azure e aprire la pagina di panoramica dell'insieme di credenziali delle chiavi. 

1. Selezionare l'impostazione **Criteri di accesso** nel riquadro di spostamento a sinistra e fare clic su Aggiungi **nuovo**.

   ![Aggiungere nuovi criteri di accesso all'insieme di credenziali delle chiaviAdd new key vault access policy](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Aggiungere nuovi criteri di accesso all'insieme di credenziali delle chiaviAdd new key vault access policy")

1. Fare clic su Seleziona entità e selezionare il servizio Ricerca cognitiva di Azure.Click **Select principal** and select your Azure Cognitive Search service. È possibile cercarlo in base al nome o all'ID oggetto visualizzato dopo l'abilitazione dell'identità gestita.

   ![Selezionare l'entità dei criteri di accesso all'insieme di credenziali delle chiaviSelect key vault access](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Selezionare l'entità dei criteri di accesso all'insieme di credenziali delle chiaviSelect key vault access")

1. Fare clic su **Autorizzazioni chiave** e selezionare *Get*, *Unwrap Key* e *Wrap Key*. È possibile usare il modello Archiviazione dati di *Azure o Archiviazione di Azure* per selezionare rapidamente le autorizzazioni necessarie.

   Ricerca cognitiva di Azure deve essere concessa con le autorizzazioni di accesso seguenti:Azure Cognitive Search must be granted with the following [access permissions:](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)

   * *Get* - consente al servizio di ricerca di recuperare le parti pubbliche della chiave in un archivio chiavi
   * *Wrap Key* - consente al servizio di ricerca di utilizzare la chiave per proteggere la chiave di crittografia interna
   * *Unwrap Key* - consente al servizio di ricerca di utilizzare la chiave per annullare il wrapping della chiave di crittografia interna

   ![Selezionare le autorizzazioni per le chiavi dei criteri di accesso all'insieme di credenziali delle chiaviSelect key policy key permissions](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Selezionare le autorizzazioni per le chiavi dei criteri di accesso all'insieme di credenziali delle chiaviSelect key policy key permissions")

1. Fare clic **su OK** e **salvare** le modifiche ai criteri di accesso.

> [!Important]
> Il contenuto crittografato in Ricerca cognitiva di Azure è configurato per usare una chiave specifica dell'insieme di chiavi di Azure con una **versione**specifica. Se si modifica la chiave o la versione, è necessario aggiornare l'indice o la mappa dei sinonimi in modo che utilizzi la nuova chiave e la nuova versione **prima** di eliminare la chiave o la versione precedente. In caso contrario, l'indice o il mapping dei sinonimi non sarà inutilizzabile, non sarà possibile decrittografare il contenuto una volta perso l'accesso alla chiave.   

## <a name="5---encrypt-content"></a>5 - Crittografare i contenuti

La creazione di un indice o di una mappa dei sinonimi crittografata con una chiave gestita dal cliente non è ancora possibile tramite il portale di Azure.Creating an index or synonym map encrypted with customer-managed key is not yet possible using Azure portal. Usare l'API REST di Ricerca cognitiva di Azure per creare un indice o una mappa di sinonimi di Azure.Use Azure Cognitive Search REST API to create an index or synonym map.

Sia la mappa dell'indice che quella dei sinonimi supportano una nuova proprietà **encryptionKey** di primo livello utilizzata per specificare la chiave. 

Utilizzando **l'URI dell'insieme**di credenziali delle chiavi, il nome della **chiave** e la versione della **chiave** dell'insieme di credenziali delle chiavi, è possibile creare una definizione **encryptionKey:**

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> Nessuno di questi dettagli dell'insieme di credenziali delle chiavi è considerato segreto e potrebbe essere facilmente recuperato accedendo alla pagina della chiave dell'insieme di credenziali delle chiavi di Azure pertinente nel portale di Azure.Nonare of these key vault details are considered secret and could be easily retrieved by browsing to the relevant Azure Key Vault key key page in Azure portal.

Se si utilizza un'applicazione AAD per l'autenticazione dell'insieme di credenziali delle chiavi anziché un'identità gestita, aggiungere **le credenziali** di accesso dell'applicazione AAD alla chiave di crittografia: 
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

## <a name="example-index-encryption"></a>Esempio: crittografia dell'indiceExample: Index encryption
I dettagli della creazione di un nuovo indice tramite l'API REST sono disponibili in Crea indice (API REST di Ricerca cognitiva di Azure), in cui l'unica differenza consiste nello specificare i dettagli della chiave di crittografia come parte della definizione dell'indice:The details of creating a new index via the REST API could be found at [Create Index (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index), where the only difference here is specifying the encryption key details as part of the index definition: 

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
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
È ora possibile inviare la richiesta di creazione dell'indice e quindi iniziare a usare l'indice normalmente.

## <a name="example-synonym-map-encryption"></a>Esempio: crittografia della mappa dei sinonimi

I dettagli della creazione di una nuova mappa dei sinonimi tramite l'API REST sono disponibili in [Create Synonym Map (Azure Cognitive Search REST API),](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)in cui l'unica differenza consiste nello specificare i dettagli della chiave di crittografia come parte della definizione della mappa dei sinonimi: 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
È ora possibile inviare la richiesta di creazione della mappa dei sinonimi e quindi iniziare a usarla normalmente.

>[!Important] 
> Mentre **encryptionKey** non può essere aggiunto agli indici di Ricerca cognitiva di Azure esistenti o alle mappe dei sinonimi, può essere aggiornato fornendo valori diversi per uno dei tre dettagli dell'insieme di credenziali delle chiavi (ad esempio, l'aggiornamento della versione della chiave). Quando si passa a una nuova chiave dell'insieme di credenziali delle chiavi o a una nuova versione della chiave, qualsiasi indice di Ricerca cognitiva di Azure o mappa di sinonimo che usa la chiave deve prima essere aggiornato per usare la nuova chiave e la versione **prima** di eliminare la chiave precedente. In caso contrario, l'indice o il mapping dei sinonimi non sarà utilizzabile, in quanto non sarà in grado di decrittografare il contenuto una volta perso l'accesso alla chiave.   
> Il ripristino delle autorizzazioni di accesso all'insieme di credenziali delle chiavi in un secondo momento ripristinerà l'accesso al contenuto.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a>Avanzate: Usare un'applicazione Azure Active Directory gestita esternamente

Quando non è possibile un'identità gestita, è possibile creare un'applicazione Azure Active Directory con un'entità di sicurezza per il servizio Ricerca cognitiva di Azure.When a managed identity is not possible, you can create an Azure Active Directory application with a security principal for your Azure Cognitive Search service. In particolare, un'identità gestita non è valida in queste condizioni:Specifically, a managed identity is not able under these conditions:

* Non è possibile concedere direttamente le autorizzazioni di accesso del servizio di ricerca all'insieme di credenziali delle chiavi, ad esempio se il servizio di ricerca si trova in un tenant di Active Directory diverso da quello dell'insieme di credenziali delle chiavi di Azure.

* Un singolo servizio di ricerca è necessario per ospitare più indici crittografati, ognuno dei quali utilizza una chiave diversa da un insieme di credenziali delle chiavi diverso, in cui ogni insieme di credenziali delle chiavi deve utilizzare **un'identità diversa** per l'autenticazione. Se non è necessario utilizzare un'identità diversa per gestire insiemi di credenziali delle chiavi diversi, è consigliabile usare l'opzione di identità gestita sopra riportata.  

Per supportare tali topologie, Ricerca cognitiva di Azure supporta l'uso di applicazioni Azure Active Directory (AAD) per l'autenticazione tra il servizio di ricerca e l'insieme di credenziali delle chiavi.    
Per creare un'applicazione AAD nel portale:To create an AAD application in the portal:

1. [Creare un'applicazione Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Ottenere l'ID applicazione e la chiave](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) di autenticazione in quanto quelli che saranno necessari per la creazione di un indice crittografato. I valori da fornire includono **l'ID applicazione** e la chiave di **autenticazione.**

>[!Important]
> Quando si decide di usare un'applicazione AAD di autenticazione anziché un'identità gestita, considerare il fatto che Ricerca cognitiva di Azure non è autorizzata a gestire l'applicazione AAD per conto dell'utente e spetta all'utente gestire l'applicazione AAD, ad esempio periodica rotazione della chiave di autenticazione dell'applicazione.
> Quando si modifica un'applicazione AAD o la relativa chiave di autenticazione, qualsiasi indice di Ricerca cognitiva di Azure o mappa di sinonimi che usa tale applicazione deve essere aggiornato per usare il nuovo ID dell'applicazione, ovvero la chiave **prima** di eliminare l'applicazione precedente o la relativa chiave di autorizzazione e prima di revocare l'accesso dell'insieme di credenziali delle chiavi.
> In caso contrario, l'indice o il mapping dei sinonimi non sarà utilizzabile, in quanto non sarà in grado di decrittografare il contenuto una volta perso l'accesso alla chiave.   

## <a name="next-steps"></a>Passaggi successivi

Se non si ha familiarità con l'architettura di sicurezza di Azure, leggere la [documentazione](https://docs.microsoft.com/azure/security/)relativa alla sicurezza di Azure e, in particolare, in questo articolo:If you are unfamiliar with Azure security architecture, review the Azure Security documentation , and in particular, this article:

> [!div class="nextstepaction"]
> [Crittografia dei dati inattivi](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
