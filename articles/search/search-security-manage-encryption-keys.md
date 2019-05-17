---
title: 'Crittografia dati inattivi con chiavi gestite dal cliente in Azure Key Vault (anteprima): ricerca di Azure'
description: Crittografia lato server supplemento su indici e le mappe sinonimiche in ricerca di Azure tramite le chiavi che vengono creati e gestiti in Azure Key Vault.
author: NatiNimni
manager: jlembicz
ms.author: natinimn
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: ''
ms.openlocfilehash: 9d2cd2a2f4b3143d58d0ef03d67de094ea03303e
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523085"
---
# <a name="azure-search-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Crittografia di ricerca di Azure con chiavi gestite dal cliente in Azure Key Vault

> [!Note]
> La crittografia con chiavi gestite dal cliente è in anteprima e non è destinata all'uso in produzione. Il [API REST versione 2019-05-06-Preview](search-api-preview.md) fornisce questa funzionalità. È anche possibile usare la versione di .NET SDK 8.0-preview.
>
> Questa funzionalità non è disponibile per i servizi gratuiti. È necessario usare un servizio di ricerca fatturabili creato a decorrere 2019-01-01. Non vi è alcun supporto per il portale in questo momento.

Per impostazione predefinita, ricerca di Azure crittografa i contenuti di utenti inattivi con la [chiavi gestite dal servizio](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest#data-encryption-models). È possibile integrare la crittografia predefinita con un livello aggiuntivo di crittografia usando le chiavi che vengono creati e gestiti in Azure Key Vault. Questo articolo illustra i passaggi.

Grazie all'integrazione con la crittografia lato server è supportata [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). È possibile creare chiavi di crittografia personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API di Azure Key Vault per generare chiavi di crittografia. Con Azure Key Vault, è inoltre possibile controllare l'utilizzo della chiave. 

La crittografia con chiavi gestite dal cliente è configurata a livello di indice o un sinonimo della mappa quando tali oggetti vengono creati e non a livello di servizio di ricerca. Non è possibile crittografare il contenuto già esistente. 

È possibile usare chiavi diverse da diversi insiemi di credenziali chiave. Ciò significa che un servizio di ricerca singola può ospitare più mappe indexes\synonym crittografato, ciascuno crittografato utilizzando potenzialmente una diversa chiave gestita dal cliente, insieme alle mappe indexes\synonym che non vengono crittografati con chiavi gestite dal cliente. 

## <a name="prerequisites"></a>Prerequisiti

In questo esempio vengono usati i servizi seguenti. 

+ [Creare un servizio Ricerca di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questa esercitazione.

+ [Creare una risorsa di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) o trovare un insieme di credenziali esistente nella sottoscrizione.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) oppure [CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) viene usato per attività di configurazione.

+ [Postman](search-fiddler.md), [Azure PowerShell](search-create-index-rest-api.md) e [Azure Search SDK](https://aka.ms/search-sdk-preview) può essere utilizzato per chiamare l'API REST in anteprima. Non vi è alcun supporto per .NET SDK per la crittografia gestite dal cliente al momento o il portale.

## <a name="1---enable-key-recovery"></a>1 - abilitare recupero chiavi

Questo passaggio è facoltativo ma consigliato. Dopo aver creato la risorsa di Azure Key Vault, abilitare **eliminazione temporanea** e **ripulire protezione** nell'insieme di credenziali chiave selezionata eseguendo i comandi di PowerShell o CLI di Azure seguenti:   

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
> A causa della natura della crittografia con funzionalità chiavi gestite dal cliente, ricerca di Azure non sarà in grado di recuperare i dati se viene eliminata la chiave di insieme di credenziali delle chiavi di Azure. Per evitare perdite di dati causate da eliminazioni accidentali di chiave dell'insieme di credenziali chiave, è consigliabile abilitare l'eliminazione temporanea e ripulire protezione nell'insieme di credenziali chiave selezionato. Per altre informazioni, vedere [eliminazione temporanea di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="2---create-a-new-key"></a>2 - creare una nuova chiave

Se si usa una chiave esistente per crittografare il contenuto di ricerca di Azure, ignorare questo passaggio.

1. [Accedere al portale di Azure](https://portal.azure.com) e passare al dashboard dell'insieme di credenziali chiave.

1. Selezionare il **tasti** impostazione nel riquadro di spostamento a sinistra e fare clic su **+ genera/Importa**.

1. Nel **creare una chiave** riquadro, dall'elenco dei **opzioni**, scegliere il metodo che si desidera utilizzare per creare una chiave. È possibile **genera** una nuova chiave **caricare** esistente della chiave oppure usare **Restore Backup** per selezionare un backup di una chiave.

1. Immettere un **nome** relative alla chiave e, facoltativamente, selezionare altre proprietà chiave.

1. Fare clic sui **Create** pulsante per avviare la distribuzione.

Prendere nota dell'identificatore di chiave: questo è composto dal **valore Uri della chiave**, il **nome della chiave**e il **versione della chiave**. È necessario per definire un indice crittografato in ricerca di Azure.
 
![Creare una nuova chiave di istanza di key vault](./media/search-manage-encryption-keys/create-new-key-vault-key.png "creare una nuova chiave di insieme di credenziali delle chiavi")

## <a name="3---create-a-service-identity"></a>3 - creare un'identità del servizio

Assegnazione di un'identità al servizio di ricerca consente di concedere autorizzazioni di accesso di Key Vault per il servizio di ricerca. Il servizio di ricerca userà la propria identità per l'autenticazione con Azure Key vault.

Ricerca di Azure supporta due modalità per l'assegnazione di identità: un'identità gestita o un'applicazione di Azure Active Directory gestito esternamente. 

Se possibile, usare un'identità gestita. È il modo più semplice di assegnazione di un'identità del servizio di ricerca e dovrebbe funzionare nella maggior parte degli scenari. Se si usa più chiavi per gli indici e le mappe sinonimiche, o se la soluzione si trova in un'architettura distribuita che invalida l'autenticazione basata sull'identità, usare l'avanzato [approccio di Azure Active Directory gestito esternamente](#aad-app)descritte alla fine di questo articolo.

 In generale, un'identità gestita consente il servizio di ricerca per l'autenticazione ad Azure Key Vault senza archiviare le credenziali nel codice. Il ciclo di vita di questo tipo di identità gestita è legato al ciclo di vita del servizio di ricerca, che può avere solo una sola identità gestita. [Altre informazioni sulle identità gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Per creare un'identità gestita, [Accedi a portale toAzure](https://portal.azure.com) e aprire il dashboard del servizio di ricerca. 

1. Fare clic su **Identity** nel riquadro di spostamento sinistro, modificarne lo stato per **sul**, fare clic su **Salva**.

![Abilitare un'identità gestita](./media/search-enable-msi/enable-identity-portal.png "abilitare un'identità gestita")

## <a name="4---grant-key-access-permissions"></a>4: concedere le autorizzazioni di accesso alle chiavi

Per abilitare il servizio di ricerca a usare la chiave di Key Vault, è necessario per la ricerca di concedere alcune autorizzazioni di accesso del servizio.

Le autorizzazioni di accesso può essere revocate in qualsiasi momento. Una volta revocato, qualsiasi ricerca indice o sinonimo mapping dei servizi che usa l'insieme di credenziali diventerà inutilizzabile. Le autorizzazioni di accesso dell'insieme di credenziali chiave di ripristino in un secondo momento verrà ripristinato accesso mappa index\synonym. Per altre informazioni, vedere [proteggere l'accesso a un insieme di credenziali delle chiavi](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Accedere al portale di Azure](https://portal.azure.com) e aprire la pagina Panoramica dell'insieme di credenziali delle chiavi. 

1. Selezionare il **criteri di accesso** impostazione nel riquadro di spostamento a sinistra e fare clic su **+ Aggiungi nuova**.

   ![Aggiungere nuovi criteri di accesso di istanza di key vault](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "aggiungere nuovi criteri di accesso dell'insieme di credenziali chiave")

1. Fare clic su **selezionare un'entità** e selezionare il servizio di ricerca di Azure. È possibile cercarlo in base al nome o dall'ID di oggetto che è stato visualizzato dopo l'abilitazione di identità gestita.

   ![Entità dei criteri di accesso selezionare insieme di credenziali delle chiavi](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "entità dei criteri di accesso selezionare insieme di credenziali delle chiavi")

1. Fare clic su **autorizzazioni chiave** e selezionare *ottenere*, *Unwrap Key* e *Wrap Key*. È possibile usare la *archiviazione di Azure Data Lake o archiviazione di Azure* modello selezionare rapidamente le autorizzazioni necessarie.

   Ricerca di Azure deve essere concesso con quanto segue [le autorizzazioni di accesso](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *Ottenere* -consente al servizio di ricerca recuperare le parti pubbliche della chiave in un Azure Key Vault
   * *Wrap Key* -consente al servizio di ricerca di usare la chiave per proteggere la chiave di crittografia interni
   * *Unwrap Key* -consente al servizio di ricerca di usare la chiave per annullare il wrapping della chiave di crittografia interni

   ![Selezionare autorizzazioni chiave dei criteri di accesso dell'insieme di credenziali chiave](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "selezionare autorizzazioni chiave dei criteri di accesso dell'insieme di credenziali chiave")

1. Fare clic su **OK** e **salvare** le modifiche ai criteri di accesso.

> [!Important]
> Il contenuto crittografato in ricerca di Azure è configurato per usare una chiave di Azure Key Vault specifica con uno specifico **versione**. Se si modifica la chiave o una versione, la mappa di sinonimi o indice deve essere aggiornata per usare il nuovo key\version **prima di** eliminando il key\version precedente. In caso contrario verrà eseguito il rendering dell'indice o sinonimo mappa inutilizzabile, si sarà in grado di decrittografare il contenuto dopo aver perso l'accesso alla chiave.   

## <a name="5---encrypt-content"></a>5 - crittografare il contenuto

Creazione di una mappa di sinonimi o indice crittografata con chiavi gestite dal cliente non è ancora possibile usare il portale di Azure. Usare l'API REST di ricerca di Azure per creare questo tipo una mappa di indice o un sinonimo.

Indice sia sinonimo eseguire il mapping di supporto per un nuovo livello superiore **encryptionKey** proprietà utilizzata per specificare la chiave. 

Usando il **insieme di credenziali delle chiavi Uri**, **nome della chiave** e il **versione della chiave** della chiave dell'insieme di credenziali chiave, è possibile creare un **encryptionKey** definizione:

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
> Nessuno di questi dettagli insieme di credenziali delle chiavi sono considerati segreto e può essere facilmente recuperati passando alla pagina pertinente chiave Azure Key Vault nel portale di Azure.

Se si usa un'applicazione AAD per l'autenticazione di Key Vault invece di usare un'identità gestita, aggiungere l'applicazione di AAD **accedere alle credenziali** per la chiave di crittografia: 
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

## <a name="example-index-encryption"></a>Esempio: Crittografia di indice
Dettagli della creazione di un nuovo indice tramite l'API REST sono stati trovati in [Create Index (API di REST del servizio di ricerca di Azure)](https://docs.microsoft.com/rest/api/searchservice/create-index), in cui l'unica differenza è che specifica i dettagli chiave di crittografia come parte della definizione dell'indice: 

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
È possibile ora inviare la richiesta di creazione dell'indice e quindi iniziare a usare l'indice normalmente.

## <a name="example-synonym-map-encryption"></a>Esempio: Crittografia di mappa di sinonimi

Vedere i dettagli della creazione di una nuova mappa sinonimica tramite l'API REST [creare la mappa di sinonimi (ricerca di Azure del servizio API REST)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), in cui l'unica differenza è che specifica i dettagli chiave di crittografia come parte della definizione di mappa di sinonimi: 

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
È possibile ora inviare la richiesta di creazione mappa di sinonimi e quindi iniziare a usarlo normalmente.

>[!Important] 
> Sebbene **encryptionKey** non è possibile aggiungere agli indici di ricerca di Azure esistenti o le mappe sinonimiche, potrebbe essere aggiornato, fornendo valori diversi per uno qualsiasi dei tre dettagli insieme di credenziali delle chiavi (ad esempio, aggiornando la versione della chiave). Quando si modifica a una nuova chiave di Key Vault o una nuova versione, qualsiasi mappa sinonimo o indice di ricerca di Azure che usa la chiave deve essere aggiornata prima di tutto per usare il nuovo key\version **prima di** eliminando il key\version precedente. In caso contrario verrà eseguito il rendering dell'indice o mappa sinonimica inutilizzabile, poiché non sarà in grado di decrittografare l'accesso una sola volta principale contenuto viene persa.   
> Ripristinando le autorizzazioni di accesso dell'insieme di credenziali chiave in un secondo momento, è possibile ripristinare l'accesso al contenuto.

## <a name="aad-app"></a> Avanzate: Usare un'applicazione esternamente gestita di Azure Active Directory

Quando un'identità gestita non è possibile, che è possibile creare un'applicazione Azure Active Directory con una sicurezza principale per il servizio di ricerca di Azure. In particolare, un'identità gestita non è praticabile in queste condizioni:

* È possibile concedere direttamente l'alla ricerca servizio le autorizzazioni di accesso per l'insieme di credenziali chiave (ad esempio, se il servizio di ricerca è in un tenant di Active Directory diverso rispetto a Azure Key Vault).

* È necessario per ospitare più crittografato indexes\synonym mappe, ognuna delle quali Usa una chiave diversa da un diverso insieme di credenziali chiave, in cui ogni insieme di credenziali delle chiavi è necessario usare un servizio di ricerca singolo **un'identità diversa** per l'autenticazione. Se si usa un'identità diversa per la gestione di diversi insiemi di credenziali delle chiavi non è un requisito, è consigliabile usare l'opzione di identità gestita precedente.  

Per gestire tali topologie, ricerca di Azure supporta l'utilizzo di applicazioni di Azure Active Directory (AAD) per l'autenticazione tra il servizio di ricerca e l'insieme di credenziali delle chiavi.    
Per creare un'applicazione AAD nel portale:

1. [Creare un'applicazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Ottenere la chiave di autenticazione e l'ID applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) come quelli sarà necessari per la creazione di un indice crittografato. È necessario fornire i valori includono **ID applicazione** e **chiave di autenticazione**.

>[!Important]
> Quando si decide di usare un'applicazione di AAD di autenticazione invece di un'identità gestita, prendere in considerazione il fatto che ricerca di Azure non è autorizzata a gestire l'applicazione di AAD per tuo conto, ed è responsabilità dell'utente per gestire l'applicazione di AAD, ad esempio la rotazione periodica della la chiave di autenticazione dell'applicazione.
> Quando si modifica la chiave di autenticazione o di un'applicazione di AAD, qualsiasi mappa sinonimo o indice di ricerca di Azure che usa quell'applicazione deve essere aggiornata prima di tutto per usare la nuova applicazione ID\key **prima di** eliminazione dell'applicazione precedente o dai relativi autorizzazione e chiave, prima di revocare l'accesso di Key Vault a esso.
> In caso contrario verrà eseguito il rendering dell'indice o mappa sinonimica inutilizzabile, poiché non sarà in grado di decrittografare l'accesso una sola volta principale contenuto viene persa.   

## <a name="next-steps"></a>Passaggi successivi

Se non si ha familiarità con l'architettura di sicurezza di Azure, consultare il [documentazione di sicurezza di Azure](https://docs.microsoft.com/azure/security/)e in particolare, questo articolo:

> [!div class="nextstepaction"]
> [Crittografia dei dati inattivi](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
