---
title: Crittografia inattiva con chiavi gestite dal cliente in Azure Key Vault (anteprima)
titleSuffix: Azure Cognitive Search
description: Integrare la crittografia lato server su indici e mappe sinonimi in Azure ricerca cognitiva tramite chiavi create e gestite in Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 94c9d94edb9a9ca3f6117bd43ab9cefe1dad52a3
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794362"
---
# <a name="content-encryption-of-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Crittografia del contenuto di Azure ricerca cognitiva usando chiavi gestite dal cliente in Azure Key Vault

> [!Note]
> La crittografia con chiavi gestite dal cliente è in anteprima e non è destinata all'uso in produzione. Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). È anche possibile usare .NET SDK versione 8,0-Preview.
>
> Questa funzionalità non è disponibile per i servizi gratuiti. È necessario usare un servizio di ricerca fatturabile creato il o dopo il 2019-01-01. Al momento non è disponibile alcun supporto per il portale.

Per impostazione predefinita, Azure ricerca cognitiva crittografa il contenuto utente inattivo con le [chiavi gestite dal servizio](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models). È possibile integrare la crittografia predefinita con un livello di crittografia aggiuntivo usando le chiavi create e gestite in Azure Key Vault. Questo articolo illustra i passaggi necessari.

La crittografia lato server è supportata tramite l'integrazione con [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). È possibile creare chiavi di crittografia personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API di Azure Key Vault per generare chiavi di crittografia. Con Azure Key Vault è anche possibile controllare l'utilizzo della chiave. 

La crittografia con chiavi gestite dal cliente viene configurata a livello di indice o sinonimo quando tali oggetti vengono creati e non nel livello di servizio di ricerca. Non è possibile crittografare il contenuto già esistente. 

È possibile usare chiavi diverse da insiemi di credenziali delle chiavi diversi. Questo significa che un singolo servizio di ricerca può ospitare più mappe indexes\synonym crittografate, ognuna delle quali è crittografata con una chiave gestita dal cliente diversa, insieme a mappe indexes\synonym che non sono crittografate con chiavi gestite dal cliente. 

## <a name="prerequisites"></a>Prerequisiti

In questo esempio vengono usati i servizi seguenti. 

+ [Creare un servizio ricerca cognitiva di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questa esercitazione.

+ [Creare una risorsa Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) o trovare un insieme di credenziali esistente nella sottoscrizione.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) o l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) viene usata per attività di configurazione.

+ Per chiamare l'API REST di anteprima, è possibile usare il [post](search-get-started-postman.md), [Azure PowerShell](search-create-index-rest-api.md) e [Azure ricerca cognitiva SDK](https://aka.ms/search-sdk-preview) . Al momento non è disponibile alcun supporto per il portale o .NET SDK per la crittografia gestita dal cliente.

## <a name="1---enable-key-recovery"></a>1-Abilita ripristino chiavi

Questo passaggio è facoltativo ma consigliato. Dopo aver creato la risorsa Azure Key Vault, abilitare l' **eliminazione** temporanea e **ripulire la protezione** nell'insieme di credenziali delle chiavi selezionato eseguendo i comandi di PowerShell o dell'interfaccia della riga di comando di Azure seguenti:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

>[!Note]
> A causa della natura della crittografia con la funzionalità chiavi gestite dal cliente, Azure ricerca cognitiva non sarà in grado di recuperare i dati se la chiave dell'insieme di credenziali delle chiavi di Azure viene eliminata. Per evitare la perdita di dati causata da eliminazioni accidentali di chiavi di Key Vault, è consigliabile abilitare l'eliminazione temporanea e ripulire la protezione nell'insieme di credenziali delle chiavi selezionato. Per ulteriori informazioni, vedere [Azure Key Vault soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="2---create-a-new-key"></a>2-creare una nuova chiave

Se si usa una chiave esistente per crittografare il contenuto di Azure ricerca cognitiva, ignorare questo passaggio.

1. [Accedere a portale di Azure](https://portal.azure.com) e passare al dashboard di Key Vault.

1. Selezionare l'impostazione **chiavi** dal riquadro di spostamento a sinistra e fare clic su **+ genera/importa**.

1. Nell'elenco di **Opzioni**del riquadro **Crea chiave** scegliere il metodo che si desidera utilizzare per creare una chiave. È possibile **generare** una nuova chiave, **caricare** una chiave esistente o usare **Restore backup** per selezionare un backup di una chiave.

1. Immettere un **nome** per la chiave e, facoltativamente, selezionare altre proprietà chiave.

1. Fare clic sul pulsante **Crea** per avviare la distribuzione.

Prendere nota dell'identificatore di chiave, che è composto dall'URI del **valore della chiave**, dal **nome della chiave**e dalla **versione della chiave**. Questi sono necessari per definire un indice crittografato in ricerca cognitiva di Azure.
 
![Creare una nuova chiave di Key Vault](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Creare una nuova chiave di Key Vault")

## <a name="3---create-a-service-identity"></a>3-creare un'identità del servizio

L'assegnazione di un'identità al servizio di ricerca consente di concedere Key Vault autorizzazioni di accesso al servizio di ricerca. Il servizio di ricerca userà la propria identità per l'autenticazione con Azure Key Vault.

Azure ricerca cognitiva supporta due modi per assegnare l'identità, ovvero un'identità gestita o un'applicazione Azure Active Directory gestita esternamente. 

Se possibile, usare un'identità gestita. È il modo più semplice per assegnare un'identità al servizio di ricerca e dovrebbe funzionare nella maggior parte degli scenari. Se si usano più chiavi per gli indici e le mappe sinonimi oppure se la soluzione si trova in un'architettura distribuita che impedisce l'autenticazione basata sull'identità, usare l' [approccio avanzato Azure Active Directory gestito dall'esterno](#aad-app) , descritto alla fine di questo articolo.

 In generale, un'identità gestita consente al servizio di ricerca di eseguire l'autenticazione a Azure Key Vault senza archiviare le credenziali nel codice. Il ciclo di vita di questo tipo di identità gestita è associato al ciclo di vita del servizio di ricerca, che può avere una sola identità gestita. [Altre informazioni sulle identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Per creare un'identità gestita, [accedere al portale di Azure](https://portal.azure.com) e aprire il dashboard del servizio di ricerca. 

1. Fare clic su **Identity** nel riquadro di spostamento a sinistra, impostare lo stato **su on**e quindi fare clic su **Save**.

![Abilitare un'identità gestita](./media/search-enable-msi/enable-identity-portal.png "Abilitare un'identità alterata")

## <a name="4---grant-key-access-permissions"></a>4-concedere le autorizzazioni di accesso alla chiave

Per consentire al servizio di ricerca di usare la chiave di Key Vault, è necessario concedere al servizio di ricerca determinate autorizzazioni di accesso.

È possibile revocare le autorizzazioni di accesso in un determinato momento. Una volta revocato, qualsiasi indice del servizio di ricerca o mappa di sinonimi che usa tale Key Vault diventerà inutilizzabile. Il ripristino delle autorizzazioni di accesso di Key Vault in un secondo momento ripristinerà l'accesso alla mappa index\synonym. Per altre informazioni, vedere [proteggere l'accesso a un insieme di](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)credenziali delle chiavi.

1. [Accedere a portale di Azure](https://portal.azure.com) e aprire la pagina Panoramica di Key Vault. 

1. Selezionare l'impostazione **criteri di accesso** nel riquadro di spostamento a sinistra e fare clic su **+ Aggiungi nuovo**.

   ![Aggiungere nuovi criteri di accesso di Key Vault](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Aggiungere nuovi criteri di accesso di Key Vault")

1. Fare clic su **Seleziona entità** e selezionare il servizio ricerca cognitiva di Azure. È possibile cercarlo in base al nome o all'ID oggetto visualizzato dopo aver abilitato l'identità gestita.

   ![Selezionare l'entità criteri di accesso dell'insieme di credenziali delle chiavi](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Selezionare l'entità criteri di accesso dell'insieme di credenziali delle chiavi")

1. Fare clic su **autorizzazioni chiave** e selezionare *Get*, *Unwrap Key* e *Wrap Key*. È possibile usare il modello di *archiviazione Azure Data Lake storage o Azure* per selezionare rapidamente le autorizzazioni necessarie.

   È necessario concedere il ricerca cognitiva di Azure con le [autorizzazioni di accesso](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)seguenti:

   * *Get* : consente al servizio di ricerca di recuperare le parti pubbliche della chiave in un Key Vault
   * *Chiave a capo* : consente al servizio di ricerca di usare la chiave per proteggere la chiave di crittografia interna
   * *Unwrap Key* : consente al servizio di ricerca di usare la chiave per annullare il wrapping della chiave di crittografia interna

   ![Selezionare le autorizzazioni chiave dei criteri di accesso di Key Vault](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Selezionare le autorizzazioni chiave dei criteri di accesso di Key Vault")

1. Fare clic su **OK** e **salvare** le modifiche ai criteri di accesso.

> [!Important]
> Il contenuto crittografato in ricerca cognitiva di Azure è configurato per l'uso di una chiave di Azure Key Vault specifica con una **versione**specifica. Se si modifica la chiave o la versione, è necessario aggiornare la mappa degli indici o dei sinonimi per usare il nuovo key\version **prima** di eliminare il key\version. precedente In caso contrario, il rendering dell'indice o della mappa dei sinonimi sarà inutilizzabile e non sarà possibile decrittografare il contenuto quando l'accesso alla chiave viene perso.   

## <a name="5---encrypt-content"></a>5-crittografare il contenuto

La creazione di una mappa di indici o sinonimi crittografata con la chiave gestita dal cliente non è ancora possibile utilizzando portale di Azure. Usare l'API REST di Azure ricerca cognitiva per creare una mappa di indici o sinonimi.

Sia l'indice che la mappa dei sinonimi supportano una nuova proprietà **encryptionKey** di primo livello utilizzata per specificare la chiave. 

Usando l' **URI**dell'insieme di credenziali delle chiavi, il **nome** della chiave e la **versione** della chiave di Key Vault, è possibile creare una definizione **encryptionKey** :

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
> Nessuno di questi dettagli dell'insieme di credenziali delle chiavi è considerato segreto e può essere facilmente recuperato passando alla pagina chiave Azure Key Vault pertinente in portale di Azure.

Se si usa un'applicazione AAD per l'autenticazione Key Vault invece di usare un'identità gestita, aggiungere le credenziali di **accesso** dell'applicazione AAD alla chiave di crittografia: 
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

## <a name="example-index-encryption"></a>Esempio: crittografia degli indici
Per informazioni dettagliate sulla creazione di un nuovo indice tramite l'API REST, vedere [create index (API REST di Azure ricerca cognitiva)](https://docs.microsoft.com/rest/api/searchservice/create-index), in cui l'unica differenza consiste nel specificare i dettagli della chiave di crittografia come parte della definizione dell'indice: 

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
È ora possibile inviare la richiesta di creazione dell'indice, quindi iniziare a utilizzare l'indice normalmente.

## <a name="example-synonym-map-encryption"></a>Esempio: crittografia mappa sinonimo

Per informazioni dettagliate sulla creazione di una nuova mappa dei sinonimi tramite l'API REST, vedere creare una mappa di sinonimi [(API REST di Azure ricerca cognitiva)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), in cui l'unica differenza consiste nel specificare i dettagli della chiave di crittografia come parte della definizione della mappa dei sinonimi: 

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
È ora possibile inviare la richiesta di creazione della mappa di sinonimi e iniziare a usarla normalmente.

>[!Important] 
> Sebbene non sia possibile aggiungere **encryptionKey** a indici o mappe sinonimi esistenti di Azure ricerca cognitiva, è possibile aggiornarli specificando valori diversi per uno dei tre dettagli dell'insieme di credenziali delle chiavi (ad esempio, l'aggiornamento della versione della chiave). Quando si passa a una nuova chiave di Key Vault o a una nuova versione della chiave, qualsiasi mappa di sinonimi o di ricerca cognitiva di Azure che usa la chiave deve prima essere aggiornata per usare il nuovo key\version **prima** di eliminare il key\version. precedente In caso contrario, il mapping dell'indice o del sinonimo sarà inutilizzabile, in quanto non sarà in grado di decrittografare il contenuto quando l'accesso alla chiave viene perso.   
> Il ripristino delle autorizzazioni di accesso di Key Vault in un secondo momento ripristinerà l'accesso al contenuto.

## <a name="aad-app"></a>Avanzate: usare un'applicazione Azure Active Directory gestita esternamente

Quando non è possibile usare un'identità gestita, è possibile creare un'applicazione Azure Active Directory con un'entità di sicurezza per il servizio ricerca cognitiva di Azure. In particolare, un'identità gestita non è valida in queste condizioni:

* Non è possibile concedere direttamente le autorizzazioni di accesso al servizio di ricerca all'insieme di credenziali delle chiavi, ad esempio se il servizio di ricerca si trova in un tenant di Active Directory diverso rispetto a quello Azure Key Vault.

* È necessario un singolo servizio di ricerca per ospitare più mappe indexes\synonym crittografate, ognuna con una chiave diversa da un insieme di credenziali delle chiavi diverso, in cui ogni insieme di credenziali delle chiavi deve usare **un'identità diversa** per l'autenticazione. Se usare un'identità diversa per gestire insiemi di credenziali delle chiavi diverse non è un requisito, provare a usare l'opzione di identità gestita riportata sopra.  

Per supportare queste topologie, Azure ricerca cognitiva supporta l'uso di applicazioni Azure Active Directory (AAD) per l'autenticazione tra il servizio di ricerca e Key Vault.    
Per creare un'applicazione AAD nel portale:

1. [Creare un'applicazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Ottenere l'ID applicazione e la chiave di autenticazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) che saranno necessari per la creazione di un indice crittografato. I valori che è necessario specificare includono l' **ID applicazione** e la **chiave di autenticazione**.

>[!Important]
> Quando si decide di usare un'applicazione AAD di autenticazione invece di un'identità gestita, tenere presente che Azure ricerca cognitiva non è autorizzato a gestire l'applicazione AAD per conto dell'utente e la gestione dell'applicazione AAD, ad esempio rotazione della chiave di autenticazione dell'applicazione.
> Quando si modifica un'applicazione AAD o la relativa chiave di autenticazione, qualsiasi mappa di sinonimi o di ricerca cognitiva di Azure che usa tale applicazione deve prima essere aggiornata per usare la nuova applicazione ID\key **prima** di eliminare l'applicazione precedente o la relativa autorizzazione e prima di revocarne l'accesso Key Vault.
> In caso contrario, il mapping dell'indice o del sinonimo sarà inutilizzabile, in quanto non sarà in grado di decrittografare il contenuto quando l'accesso alla chiave viene perso.   

## <a name="next-steps"></a>Passaggi successivi

Se non si ha familiarità con l'architettura della sicurezza di Azure, vedere la [documentazione sulla sicurezza di Azure](https://docs.microsoft.com/azure/security/)e in particolare questo articolo:

> [!div class="nextstepaction"]
> [Crittografia dei dati inattivi](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
