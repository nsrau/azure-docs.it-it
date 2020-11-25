---
title: Ottimizzare i costi automatizzando i livelli di accesso all'archivio BLOB di Azure
description: Creare regole automatiche per lo stato di trasferimento dei dati tra livelli ad accesso frequente, ad accesso sporadico e archivio.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/29/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: 1b568687ffe646a91544c1bb75d26d552a23f49c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96005283"
---
# <a name="optimize-costs-by-automating-azure-blob-storage-access-tiers"></a>Ottimizzare i costi automatizzando i livelli di accesso all'archivio BLOB di Azure

I set di dati hanno cicli di vita specifici. All'inizio del ciclo di vita, gli utenti accedono ad alcuni dati spesso. La necessità di accesso si riduce tuttavia drasticamente con l'invecchiamento dei dati. Alcuni dati rimangono inattivi nel cloud e gli utenti vi accedono raramente dopo l'archiviazione. Alcuni dati scadono giorni o mesi dopo la creazione, mentre altri set di dati vengono letti e modificati per l'intero ciclo di vita. Gestione del ciclo di vita dell'archiviazione BLOB di Azure offre un criterio completo basato su regole per gli account di archiviazione BLOB e GPv2. Usare i criteri per trasferire i dati ai livelli di accesso appropriati o farli scadere alla fine del loro ciclo di vita.

I criteri di gestione del ciclo di vita consentono di eseguire queste operazioni:

- Transizione di BLOB da accesso sporadico ad accesso frequente se si accede per ottimizzare le prestazioni 
- Eseguire la transizione di BLOB, versioni BLOB e snapshot BLOB a un livello di archiviazione più freddo (accesso frequente ad accesso sporadico, da accesso frequente ad archivio o da accesso sporadico ad Archivio) se non si accede o si modifica per un determinato periodo di tempo per ottimizzare i costi
- Eliminare BLOB, versioni BLOB e snapshot BLOB alla fine del ciclo di vita
- Definire le regole da eseguire una volta al giorno a livello di account di archiviazione
- Applicare regole a contenitori o a un subset di BLOB (usando i prefissi dei nomi o i [tag degli indici BLOB](storage-manage-find-blobs.md) come filtri)

Si consideri uno scenario in cui i dati ottengono un accesso frequente durante le fasi iniziali del ciclo di vita, ma solo occasionalmente dopo due settimane. Oltre il primo mese, l'accesso al set di dati è raro. In questo scenario è consigliabile l'archiviazione ad accesso frequente durante le fasi iniziali. L'archiviazione ad accesso sporadico è più appropriata per l'accesso occasionale. Storage Archive è l'opzione del livello migliore dopo le età dei dati in un mese. Grazie alla regolazione dei livelli di archiviazione in base alla validità dei dati, è possibile progettare le opzioni di archiviazione meno costose per le proprie esigenze. Per realizzare questa transizione, sono disponibili regole dei criteri di gestione del ciclo di vita per spostare i dati a livelli di archiviazione ad accesso più sporadico.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

>[!NOTE]
>Se è necessario che i dati siano leggibili, ad esempio quando vengono usati da StorSimple, non impostare un criterio per spostare i BLOB nel livello archivio.

## <a name="availability-and-pricing"></a>Disponibilità e prezzi

La funzionalità di gestione del ciclo di vita è disponibile in tutte le aree di Azure per gli account per utilizzo generico V2 (GPv2), gli account di archiviazione BLOB, gli account di archiviazione BLOB in blocchi Premium e gli account di Azure Data Lake Storage Gen2. Nella portale di Azure è possibile aggiornare un account di per utilizzo generico esistente (utilizzo generico V1) a un account GPv2. Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).

La funzionalità di gestione del ciclo di vita è gratuita. Ai clienti viene addebitato il costo normale dell'operazione per le chiamate all'API del [livello BLOB](/rest/api/storageservices/set-blob-tier) . L'operazione di eliminazione è gratuita. Per altre informazioni sui prezzi, vedere [Prezzi dei BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="add-or-remove-a-policy"></a>Aggiungere o rimuovere un criterio

È possibile aggiungere, modificare o rimuovere un criterio utilizzando uno dei metodi seguenti:

* [Azure portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)
* [API REST](/rest/api/storagerp/managementpolicies)

Un criterio può essere letto o scritto completamente. Gli aggiornamenti parziali non sono supportati. 

> [!NOTE]
> Se per l'account di archiviazione si abilitano regole firewall, è possibile che le richieste di gestione del ciclo di vita vengano bloccate. È possibile sbloccare queste richieste fornendo eccezioni per i servizi Microsoft attendibili. Per altre informazioni, vedere la sezione Eccezioni in [Configurare i firewall e le reti virtuali](../common/storage-network-security.md#exceptions).

Questo articolo illustra come gestire i criteri usando il portale e i metodi di PowerShell.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Esistono due modi per aggiungere un criterio tramite il portale di Azure. 

* [Visualizzazione elenco portale di Azure](#azure-portal-list-view)
* [Visualizzazione codice portale di Azure](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Visualizzazione elenco portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nella portale di Azure cercare e selezionare l'account di archiviazione. 

1. In **servizio BLOB** selezionare **gestione del ciclo** di vita per visualizzare o modificare le regole.

1. Selezionare la scheda **visualizzazione elenco** .

1. Selezionare **Aggiungi una regola** e assegnare un nome alla regola nel modulo **Dettagli** . È anche possibile impostare l' **ambito della regola**, il **tipo di BLOB** e i valori dei **sottotipi di BLOB** . Nell'esempio seguente viene impostato l'ambito per filtrare i BLOB. In questo modo viene aggiunta la scheda **set di filtri** .

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-details.png" alt-text="Gestione del ciclo di vita aggiungere una pagina Dettagli regola in portale di Azure":::

1. Selezionare **BLOB di base** per impostare le condizioni per la regola. Nell'esempio seguente i BLOB vengono spostati nell'archiviazione ad accesso sporadico se non sono stati modificati per 30 giorni.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-base-blobs.png" alt-text="Pagina BLOB di base di gestione del ciclo di vita in portale di Azure":::

   L' **ultima opzione accessibile** è disponibile in anteprima nelle aree seguenti:

    - Francia centrale
    - Canada orientale
    - Canada centrale

   > [!IMPORTANT]
   > L'anteprima del rilevamento dell'ora dell'ultimo accesso è solo per uso non di produzione. I contratti di servizio (SLA) di produzione non sono al momento disponibili.
   
   Per usare l'opzione **ultimo** accesso, selezionare **rilevamento accessi abilitato** nella pagina di **gestione del ciclo** di vita del portale di Azure. Per altre informazioni sull'opzione dell' **Ultimo accesso** , vedere [spostare i dati in base alla data dell'ultimo accesso (anteprima)](#move-data-based-on-last-accessed-date-preview).

1. Se è stata selezionata l'opzione **limita BLOB con filtri** nella pagina **Dettagli** , selezionare **filtro impostato** per aggiungere un filtro facoltativo. Nell'esempio seguente vengono filtrati i BLOB nel contenitore *mylifecyclecontainer* che iniziano con "log".

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-filter-set.png" alt-text="Pagina set di filtri di gestione del ciclo di vita in portale di Azure":::

1. Selezionare **Aggiungi** per aggiungere il nuovo criterio.

#### <a name="azure-portal-code-view"></a>Visualizzazione codice portale di Azure
1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nella portale di Azure cercare e selezionare l'account di archiviazione.

1. In **servizio BLOB** selezionare **gestione del ciclo** di vita per visualizzare o modificare i criteri.

1. Il codice JSON seguente è un esempio di criteri che possono essere incollati nella scheda **visualizzazione codice** .

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "mylifecyclecontainer/log"
             ]
           }
         }
       }
     ]
   }
   ```

1. Selezionare **Salva**.

1. Per altre informazioni su questo esempio JSON, vedere le sezioni [criteri](#policy) e [regole](#rules) .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Lo script di PowerShell seguente può essere usato per aggiungere un criterio all'account di archiviazione. La variabile `$rgname` deve essere inizializzata con il nome del gruppo di risorse. La variabile `$accountName` deve essere inizializzata con il nome dell'account di archiviazione.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

# <a name="template"></a>[Modello](#tab/template)

È possibile definire la gestione del ciclo di vita usando Azure Resource Manager modelli. Ecco un modello di esempio per distribuire un account di archiviazione RA-GRS GPv2 con criteri di gestione del ciclo di vita.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

---

## <a name="policy"></a>Criteri

I criteri di gestione del ciclo di vita sono una raccolta di regole in un documento JSON:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

Un criterio è una raccolta di regole:

| Nome parametro | Tipo di parametro | Note |
|----------------|----------------|-------|
| `rules`        | Una matrice di oggetti regola | È necessario almeno una regola in un criterio. È possibile definire fino a 100 regole in un criterio.|

Ogni regola all'interno del criterio presenta diversi parametri:

| Nome parametro | Tipo di parametro | Note | Necessario |
|----------------|----------------|-------|----------|
| `name`         | string |Il nome di una regola può includere fino a 256 caratteri alfanumerici. Nel nome della regola viene applicata la distinzione tra maiuscole e minuscole. Il nome deve essere univoco nel criterio. | True |
| `enabled`      | Boolean | Valore booleano facoltativo per consentire la disabilitazione temporanea di una regola. Il valore predefinito è true se non è impostato. | False | 
| `type`         | Un valore di enumerazione | Il tipo valido corrente è `Lifecycle` . | True |
| `definition`   | Un oggetto che definisce la regola del ciclo di vita | Ogni definizione è composta da un set di filtri e un set di azioni. | True |

## <a name="rules"></a>Regole

La definizione di ogni regola include un set di filtri e un set di azioni. Il [set di filtri](#rule-filters) limita le azioni della regola a un determinato set di oggetti all'interno di un contenitore o di nomi di oggetti. Il [set di azioni](#rule-actions) applica le azioni tier o delete al set filtrato di oggetti.

### <a name="sample-rule"></a>Regola di esempio

La regola di esempio seguente Filtra l'account per eseguire le azioni sugli oggetti presenti all'interno di `container1` e iniziare con `foo` .

>[!NOTE]
>- La gestione del ciclo di vita supporta i tipi BLOB in blocchi e di Accodamento.<br>
>- La gestione del ciclo di vita non influisce sui contenitori di sistema come $logs e $web.

- Impostare il BLOB sul livello di accesso sporadico 30 giorni dopo l'ultima modifica
- Impostare il BLOB sul livello archivio 90 giorni dopo l'ultima modifica
- Eliminare il BLOB 2.555 giorni (7 anni) dopo l'ultima modifica
- Elimina le versioni BLOB precedenti 90 giorni dopo la creazione

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "rulefoo",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "delete": {
              "daysAfterCreationGreaterThan": 90
            }
          },
          "baseBlob": {
            "tierToCool": {
              "daysAfterModificationGreaterThan": 30
            },
            "tierToArchive": {
              "daysAfterModificationGreaterThan": 90
            },
            "delete": {
              "daysAfterModificationGreaterThan": 2555
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "container1/foo"
          ]
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Filtri della regola

I filtri limitano le azioni della regola a un sottoinsieme di BLOB all'interno dell'account di archiviazione. Se viene definito più di un filtro, viene eseguito un `AND` logico su tutti i filtri.

I filtri includono:

| Nome filtro | Tipo di filtro | Note | Obbligatorio |
|-------------|-------------|-------|-------------|
| blobTypes   | Una matrice di valori di enumerazione predefiniti. | La versione corrente supporta `blockBlob` e `appendBlob` . Solo l'eliminazione è supportata per `appendBlob` , il livello set non è supportato. | Sì |
| prefixMatch | Matrice di stringhe per i prefissi da confrontare. Ogni regola può definire un massimo di 10 prefissi. Una stringa di prefisso deve iniziare con un nome di contenitore. Se ad esempio si desidera trovare la corrispondenza di tutti i BLOB in `https://myaccount.blob.core.windows.net/container1/foo/...` per una regola, prefixMatch è `container1/foo` . | Se non si definisce prefixMatch, la regola si applica a tutti i BLOB all'interno dell'account di archiviazione. | No |
| blobIndexMatch | Matrice di valori del dizionario costituito da condizioni di chiave e valore del tag di indice BLOB da confrontare. Ogni regola può definire fino a 10 condizioni di tag di indice BLOB. Se ad esempio si desidera trovare la corrispondenza di tutti i BLOB con `Project = Contoso` in `https://myaccount.blob.core.windows.net/` per una regola, blobIndexMatch è `{"name": "Project","op": "==","value": "Contoso"}` . | Se non si definisce blobIndexMatch, la regola si applica a tutti i BLOB all'interno dell'account di archiviazione. | No |

> [!NOTE]
> L'indice BLOB è in anteprima pubblica ed è disponibile nelle aree **Canada centrale**, **Canada orientale**, **Francia centrale** e **Francia meridionale** . Per altre informazioni su questa funzionalità insieme ai problemi noti e alle limitazioni, vedere [Gestire e trovare i dati nell'archiviazione BLOB di Azure con l'indice BLOB (anteprima)](storage-manage-find-blobs.md).

### <a name="rule-actions"></a>Azioni della regola

Le azioni vengono applicate ai BLOB filtrati quando viene soddisfatta la condizione di esecuzione.

La gestione del ciclo di vita supporta la suddivisione in livelli e l'eliminazione di BLOB, versioni precedenti di BLOB e snapshot BLOB. Definire almeno un'azione per ogni regola sui BLOB di base, sulle versioni precedenti dei BLOB o sugli snapshot BLOB.

| Azione                      | BLOB di base                                  | Snapshot      | Versione
|-----------------------------|--------------------------------------------|---------------|---------------|
| tierToCool                  | Supportato per `blockBlob`                  | Supportato     | Supportato     |
| enableAutoTierToHotFromCool | Supportato per `blockBlob`                  | Non supportate | Non supportate |
| tierToArchive               | Supportato per `blockBlob`                  | Supportato     | Supportato     |
| eliminare                      | Supportato per `blockBlob` e `appendBlob` | Supportato     | Supportato     |

>[!NOTE]
>Se nello stesso BLOB è stata definita più di un'azione, la gestione del ciclo di vita applica al BLOB l'azione meno costosa. Ad esempio, l'azione `delete` è meno costosa dell'azione `tierToArchive`. L'azione `tierToArchive` è meno costosa dell'azione `tierToCool`.

Le condizioni di esecuzione sono basate sull'età. I BLOB di base usano l'ora dell'Ultima modifica, le versioni dei BLOB usano l'ora di creazione della versione e gli snapshot BLOB usano l'ora di creazione dello snapshot per tenere traccia dell'età.

| Condizione di esecuzione azione               | Valore della condizione                          | Descrizione                                                                      |
|------------------------------------|------------------------------------------|----------------------------------------------------------------------------------|
| daysAfterModificationGreaterThan   | Valore intero che indica il tempo trascorso in giorni | Condizione per le azioni BLOB di base                                              |
| daysAfterCreationGreaterThan       | Valore intero che indica il tempo trascorso in giorni | Condizione per le azioni di versione BLOB e snapshot BLOB                         |
| daysAfterLastAccessTimeGreaterThan | Valore intero che indica il tempo trascorso in giorni | anteprima Condizione per le azioni BLOB di base quando è abilitata l'ora dell'ultimo accesso |

## <a name="examples"></a>Esempio

Gli esempi seguenti illustrano come affrontare scenari comuni relativi alle regole dei criteri del ciclo di vita.

### <a name="move-aging-data-to-a-cooler-tier"></a>Spostare i dati a un livello di archiviazione ad accesso più sporadico

Questo esempio illustra la transizione di BLOB in blocchi con prefisso `container1/foo` o `container2/bar`. Il criterio sposta i BLOB che non sono stati modificati da oltre 30 giorni a un livello di archiviazione ad accesso sporadico e i BLOB non modificati da 90 giorni a un livello di archiviazione archivio:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="move-data-based-on-last-accessed-date-preview"></a>Spostare i dati in base alla data dell'ultimo accesso (anteprima)

È possibile abilitare il rilevamento dell'ora dell'ultimo accesso per tenere traccia del momento in cui l'ultima lettura o scrittura del BLOB. È possibile usare l'ora dell'ultimo accesso come filtro per gestire la suddivisione in livelli e la conservazione dei dati BLOB.

L' **ultima opzione accessibile** è disponibile in anteprima nelle aree seguenti:

 - Francia centrale
 - Canada orientale
 - Canada centrale

> [!IMPORTANT]
> L'anteprima del rilevamento dell'ora dell'ultimo accesso è solo per uso non di produzione. I contratti di servizio (SLA) di produzione non sono al momento disponibili.

Per usare l'opzione **ultimo** accesso, selezionare **rilevamento accessi abilitato** nella pagina di **gestione del ciclo** di vita del portale di Azure.

#### <a name="how-last-access-time-tracking-works"></a>Funzionamento dell'ultimo rilevamento del tempo di accesso

Quando è abilitata l'ultima verifica del tempo di accesso, la proprietà BLOB denominata `LastAccessTime` viene aggiornata quando un BLOB viene letto o scritto. Un'operazione [Get BLOB](/rest/api/storageservices/get-blob) è considerata un'operazione di accesso. [Ottenere le proprietà del BLOB](/rest/api/storageservices/get-blob-properties), [ottenere i metadati](/rest/api/storageservices/get-blob-metadata)del BLOB e [ottenere i tag BLOB](/rest/api/storageservices/get-blob-tags) non sono operazioni di accesso e pertanto non aggiornare l'ora dell'ultimo accesso.

Per ridurre al minimo l'effetto sulla latenza di accesso in lettura, solo la prima lettura delle ultime 24 ore aggiorna l'ora dell'ultimo accesso. Le letture successive nello stesso periodo di 24 ore non aggiornano l'ora dell'ultimo accesso. Se un BLOB viene modificato tra le letture, l'ora dell'ultimo accesso è più recente dei due valori.

Nell'esempio seguente i BLOB vengono spostati nell'archiviazione ad accesso sporadico se non sono stati usati per 30 giorni. La `enableAutoTierToHotFromCool` proprietà è un valore booleano che indica se un BLOB deve essere automaticamente suddiviso in livelli dal livello di accesso sporadico a quello di accesso frequente se si accede di nuovo a livelli a freddo.

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

#### <a name="storage-account-support"></a>Supporto dell'account di archiviazione

Il rilevamento dell'ora dell'ultimo accesso è disponibile per i tipi di account di archiviazione seguenti:

 - Account di archiviazione per utilizzo generico V2
 - Bloccare gli account di archiviazione BLOB
 - Account di archiviazione BLOB

Se l'account di archiviazione è un account per utilizzo generico V1, usare il portale di Azure per eseguire l'aggiornamento a un account per utilizzo generico V2.

Sono ora supportati gli account di archiviazione con uno spazio dei nomi gerarchico abilitato per l'uso con Azure Data Lake Storage Gen2.

#### <a name="pricing-and-billing"></a>Prezzi e fatturazione

Ogni ultimo aggiornamento del tempo di accesso è considerato un' [altra operazione](https://azure.microsoft.com/pricing/details/storage/blobs/).

### <a name="archive-data-after-ingest"></a>Archiviare i dati dopo l'inserimento

Alcuni dati rimangono inattivi sul cloud e gli utenti vi accedono raramente, se non mai, dopo l'archiviazione. I criteri del ciclo di vita seguenti sono configurati per archiviare i dati subito dopo l'inserimento. Questo esempio esegue la transizione dei BLOB in blocchi nell'account di archiviazione all'interno del contenitore `archivecontainer` in un livello archivio. La transizione viene eseguita agendo sui BLOB 0 giorni dopo l'ora dell'Ultima modifica:

> [!NOTE] 
> È consigliabile caricare i BLOB direttamente nel livello Archivio per essere più efficienti. È possibile usare l'intestazione x-MS-Access-Tier per [PutBlob](/rest/api/storageservices/put-blob) o [PutBlockList](/rest/api/storageservices/put-block-list) con REST versione 2018-11-09 e più recente o le librerie client di archiviazione BLOB più recenti. 

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Impostare la scadenza dei dati in base al tempo trascorso

Si prevede che alcuni dati scadano giorni o mesi dopo la creazione. È possibile configurare un criterio di gestione del ciclo di vita per impostare la scadenza dei dati tramite eliminazione in base al tempo trascorso. L'esempio seguente mostra un criterio che elimina tutti i BLOB in blocchi più vecchi di 365 giorni.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-data-with-blob-index-tags"></a>Eliminare dati con tag di indice BLOB
Alcuni dati devono essere scaduti solo se contrassegnati in modo esplicito per l'eliminazione. È possibile configurare i criteri di gestione del ciclo di vita in modo che scadano i dati contrassegnati con gli attributi chiave/valore dell'indice BLOB. L'esempio seguente illustra un criterio che elimina tutti i BLOB in blocchi contrassegnati con `Project = Contoso` . Per altre informazioni sull'indice BLOB, vedere [Gestire e trovare i dati nell'archiviazione BLOB di Azure con l'indice BLOB (anteprima)](storage-manage-find-blobs.md).

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="manage-versions"></a>Gestire le versioni

Per i dati modificati e a cui si accede regolarmente per tutta la sua durata, è possibile abilitare il controllo delle versioni dell'archiviazione BLOB in modo da mantenere automaticamente le versioni precedenti di un oggetto. È possibile creare un criterio per il livello o eliminare le versioni precedenti. La validità della versione è determinata dalla valutazione dell'ora di creazione della versione. Questa regola dei criteri consente di livelli le versioni precedenti all'interno del contenitore `activedata` che sono 90 giorni o precedenti dopo la creazione della versione a livello di accesso sporadico ed Elimina le versioni precedenti che sono di 365 giorni o meno recenti.

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "versionrule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "tierToCool": {
              "daysAfterCreationGreaterThan": 90
            },
            "delete": {
              "daysAfterCreationGreaterThan": 365
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "activedata"
          ]
        }
      }
    }
  ]
}
```

## <a name="faq"></a>Domande frequenti

**Ho creato un nuovo criterio, perché le azioni non vengono eseguite immediatamente?**

La piattaforma esegue i criteri di gestione del ciclo di vita una volta al giorno. Una volta configurati i criteri, possono essere necessarie fino a 24 ore prima che alcune azioni vengano eseguite per la prima volta.

**Se si aggiorna un criterio esistente, quanto tempo è necessario per l'esecuzione delle azioni?**

Il criterio aggiornato richiede fino a 24 ore per diventare effettivo. Una volta attivati i criteri, potrebbero essere necessarie fino a 24 ore per l'esecuzione delle azioni. Per il completamento delle azioni dei criteri, quindi, potrebbero essere necessarie fino a 48 ore.

**Ho reidratato manualmente un BLOB archiviato. come posso impedire che venga spostato di nuovo nel livello di archiviazione temporaneamente?**

Quando un BLOB viene spostato da un livello di accesso a un altro, l'ora dell'Ultima modifica non cambia. Se si riattiva manualmente un BLOB archiviato in un livello di accesso frequente, questo verrà spostato di nuovo al livello archivio dal motore di gestione del ciclo di vita. Disabilitare temporaneamente la regola che influisca su questo BLOB per impedirne l'archiviazione. Abilitare nuovamente la regola quando il BLOB può essere spostato di nuovo in modo sicuro nel livello archivio. È anche possibile copiare il BLOB in un altro percorso se deve rimanere in modo permanente ad accesso frequente o ad accesso sporadico.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come recuperare i dati dopo l'eliminazione accidentale:

- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](./soft-delete-blob-overview.md)

Informazioni su come gestire e trovare i dati con l'indice BLOB:

- [Gestire e trovare i dati nell'archivio BLOB di Azure con indice BLOB](storage-manage-find-blobs.md)