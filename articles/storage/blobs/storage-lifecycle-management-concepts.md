---
title: Gestire il ciclo di vita di archiviazione di Azure
description: Informazioni su come creare regole dei criteri del ciclo di vita per la transizione dei dati da livelli di archiviazione ad accesso frequente a livelli di archiviazione ad accesso sporadico e archivio.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: common
ms.openlocfilehash: 26ff592ea0d0a57049ae11a981fe8d8e77ca876f
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606954"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Gestire il ciclo di vita di archiviazione Blob di Azure

I set di dati hanno cicli di vita specifici. All'inizio del ciclo di vita, gli utenti accedono ad alcuni dati spesso. La necessità di accesso si riduce tuttavia drasticamente con l'invecchiamento dei dati. Alcuni dati rimangono inattivi nel cloud e gli utenti vi accedono raramente dopo l'archiviazione. Alcuni dati scadono giorni o mesi dopo la creazione, mentre altri set di dati vengono letti e modificati per l'intero ciclo di vita. Gestione del ciclo di vita di Azure Blob storage offre un criterio avanzato, basato su regole per gli account di archiviazione per utilizzo generico v2 e Blob. Usare i criteri per trasferire i dati ai livelli di accesso appropriati o farli scadere alla fine del loro ciclo di vita.

I criteri di gestione del ciclo di vita consentono di eseguire queste operazioni:

- Spostare i BLOB a un livello di archiviazione ad accesso più sporadico (da frequente a sporadico, da frequente ad archivio o da sporadico ad archivio) per ottimizzare costi e prestazioni
- Eliminare i BLOB al termine del ciclo di vita
- Definire le regole da eseguire una volta al giorno a livello di account di archiviazione
- Applicare regole ai contenitori o a un sottoinsieme di BLOB (usando i prefissi come filtri)

Si consideri uno scenario in cui i dati ottiene accesso frequente durante le fasi iniziali del ciclo di vita, ma solo in alcuni casi dopo due settimane. Oltre il primo mese, l'accesso al set di dati è raro. In questo scenario è consigliabile l'archiviazione ad accesso frequente durante le fasi iniziali. Archiviazione ad accesso sporadico è particolarmente appropriato per l'accesso occasionale. Spazio di archiviazione è la migliore opzione di livello dopo la data di età più di un mese. Grazie alla regolazione dei livelli di archiviazione in base alla validità dei dati, è possibile progettare le opzioni di archiviazione meno costose per le proprie esigenze. Per realizzare questa transizione, sono disponibili regole dei criteri di gestione del ciclo di vita per spostare i dati a livelli di archiviazione ad accesso più sporadico.

## <a name="storage-account-support"></a>Supporto dell'account di archiviazione

I criteri di gestione del ciclo di vita sono disponibili sia con gli account per utilizzo generico v2 (GPv2), sia con gli account di archiviazione BLOB. Nel portale di Azure, è possibile aggiornare un account di uso generale (per utilizzo generico v1) esistente a un account per utilizzo generico v2. Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).  

## <a name="pricing"></a>Prezzi

La funzionalità di gestione del ciclo di vita è gratuita. Ai clienti viene addebitato il normale costo dell'operazione per le chiamate API [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) (Elenca BLOB) e [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) (Imposta livello BLOB). Operazione di eliminazione è gratuita. Per altre informazioni sui prezzi, vedere [Prezzi dei BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Disponibilità a livello di area

La funzionalità di gestione del ciclo di vita è disponibile in tutte le aree globali di Azure.

## <a name="add-or-remove-a-policy"></a>Aggiungere o rimuovere un criterio

È possibile aggiungere, modificare o rimuovere un criterio utilizzando uno dei metodi seguenti:

* [Portale di Azure](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [API REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Questo articolo illustra come gestire i criteri usando il portale e i metodi di PowerShell.  

> [!NOTE]
> Se per l'account di archiviazione si abilitano regole firewall, è possibile che le richieste di gestione del ciclo di vita vengano bloccate. È possibile sbloccare queste richieste specificando eccezioni. Il bypass necessario sono: `Logging,  Metrics,  AzureServices`. Per altre informazioni, vedere la sezione Eccezioni in [Configurare i firewall e le reti virtuali](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **tutte le risorse** e quindi selezionare l'account di archiviazione.

3. Sotto **servizio Blob**, selezionare **Lifecycle management** per visualizzare o modificare i criteri.

4. Il codice JSON seguente è un esempio di una regola che può essere incollato nel **gestione del ciclo di vita** pagina del portale.

   ```json
   {
     "rules": [
       {
         "name": "ruleFoo",
         "enabled": true,
         "type": "Lifecycle",
         "definition": {
           "filters": {
             "blobTypes": [ "blockBlob" ],
             "prefixMatch": [ "container1/foo" ]
           },
           "actions": {
             "baseBlob": {
               "tierToCool": { "daysAfterModificationGreaterThan": 30 },
               "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
               "delete": { "daysAfterModificationGreaterThan": 2555 }
             },
             "snapshot": {
               "delete": { "daysAfterCreationGreaterThan": 90 }
             }
           }
         }
       }
     ]
   }
   ```

5. Per altre informazioni su questo esempio JSON, vedere la [criterio](#policy) e [regole](#rules) sezioni.

### <a name="powershell"></a>PowerShell

Il seguente script PowerShell è utilizzabile per aggiungere un criterio per l'account di archiviazione. Il `$rgname` variabile deve essere inizializzata con il nome del gruppo di risorse. Il `$accountName` variabile deve essere inizializzata con il nome di account di archiviazione.

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
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

## <a name="azure-resource-manager-template-with-lifecycle-management-policy"></a>Modello di Azure Resource Manager con i criteri di gestione del ciclo di vita

È possibile definire la gestione del ciclo di vita con i modelli di Azure Resource Manager. Ecco un modello di esempio per distribuire un account di archiviazione RA-GRS per utilizzo generico v2 con un criterio di gestione del ciclo di vita.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
| `rules`        | Una matrice di oggetti regola | È necessaria almeno una regola in un criterio. È possibile definire fino a 100 regole in un criterio.|

Ogni regola all'interno del criterio ha diversi parametri:

| Nome parametro | Tipo di parametro | Note | Obbligatorio |
|----------------|----------------|-------|----------|
| `name`         | String |Nome di una regola può includere fino a 256 caratteri alfanumerici. Nel nome della regola viene applicata la distinzione tra maiuscole e minuscole.  Il nome deve essere univoco nel criterio. | True  |
| `enabled`      | Boolean | Un valore booleano facoltativo per consentire una regola affinché sia temporanea è disabilitata. Valore predefinito è true se non è impostata. | Falso | 
| `type`         | Un valore di enumerazione | Il tipo valido corrente è `Lifecycle`. | True  |
| `definition`   | Un oggetto che definisce la regola del ciclo di vita | Ogni definizione è composta da un set di filtri e un set di azioni. | True  |

## <a name="rules"></a>Regole

La definizione di ogni regola include un set di filtri e un set di azioni. Il [set di filtri](#rule-filters) limita le azioni della regola a un determinato set di oggetti all'interno di un contenitore o di nomi di oggetti. Il [set di azioni](#rule-actions) applica le azioni tier o delete al set filtrato di oggetti.

### <a name="sample-rule"></a>Regola di esempio

La regola di esempio seguente filtra l'account per eseguire le azioni su oggetti presenti all'interno `container1` a partire `foo`.  

- Impostare il BLOB sul livello di accesso sporadico 30 giorni dopo l'ultima modifica
- Impostare il BLOB sul livello archivio 90 giorni dopo l'ultima modifica
- Eliminare il BLOB 2.555 giorni (7 anni) dopo l'ultima modifica
- Elimina gli snapshot del BLOB 90 giorni dopo la creazione dello snapshot

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Filtri della regola

I filtri limitano le azioni della regola a un sottoinsieme di BLOB all'interno dell'account di archiviazione. Se viene definito più di un filtro, viene eseguito un `AND` logico su tutti i filtri.

I filtri includono:

| Nome filtro | Tipo filtro | Note | Obbligatorio |
|-------------|-------------|-------|-------------|
| blobTypes   | Una matrice di valori di enumerazione predefiniti. | La versione corrente supporta `blockBlob`. | Sì |
| prefixMatch | Una matrice di stringhe per i prefissi corrispondenti. Ogni regola può definire prefissi fino a 10. Una stringa di prefisso deve iniziare con un nome di contenitore. Ad esempio, se si desidera che in tutti i BLOB in modo che corrispondano `https://myaccount.blob.core.windows.net/container1/foo/...` per una regola, è il prefixMatch `container1/foo`. | Se non si definisce prefixMatch, la regola si applica a tutti i BLOB nell'account di archiviazione.  | N. |

### <a name="rule-actions"></a>Azioni regola

Le azioni vengono applicate ai BLOB filtrati quando viene soddisfatta la condizione di esecuzione.

Gestione del ciclo di vita supporta la suddivisione in livelli e l'eliminazione di BLOB e l'eliminazione degli snapshot di blob. Definire almeno un'azione per ogni regola sui BLOB o sugli snapshot dei BLOB.

| Azione        | BLOB di base                                   | Snapshot      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Supporta i BLOB attualmente al livello di archiviazione ad accesso frequente         | Non supportate |
| tierToArchive | Supporta i BLOB attualmente al livello di archiviazione ad accesso frequente o sporadico | Non supportate |
| delete        | Supportato                                   | Supportato     |

>[!NOTE]
>Se nello stesso BLOB è stata definita più di un'azione, la gestione del ciclo di vita applica al BLOB l'azione meno costosa. Ad esempio, l'azione `delete` è meno costosa dell'azione `tierToArchive`. L'azione `tierToArchive` è meno costosa dell'azione `tierToCool`.

Le condizioni di esecuzione sono basate in base ad age. Per tenere traccia del tempo trascorso, i BLOB di base usano la data/ora dell'ultima modifica, mentre gli snapshot dei BLOB usano la data/ora di creazione dello snapshot.

| Azione Esegui condizione             | Valore della condizione                          | Descrizione                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | Valore intero che indica il tempo trascorso in giorni | La condizione per le azioni di blob di base     |
| daysAfterCreationGreaterThan     | Valore intero che indica il tempo trascorso in giorni | La condizione per le azioni di snapshot di blob |

## <a name="examples"></a>Esempi

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

### <a name="archive-data-at-ingest"></a>Archiviare i dati al momento dell'inserimento

Alcuni dati rimangono inattivi sul cloud e gli utenti vi accedono raramente, se non mai, dopo l'archiviazione. I seguenti criteri di ciclo di vita sono configurato per archiviare i dati una volta di inserimento. In questo esempio i BLOB nell'account di archiviazione all'interno di contenitori in blocchi transizioni `archivecontainer` in un livello di spazio di archiviazione. La transizione avviene tramite che agisce sui blob di 0 giorni dopo l'ora dell'ultima modifica:

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

Sono previsto che alcuni dati scadono giorni o mesi dopo la creazione. È possibile configurare un criterio di gestione del ciclo di vita per impostare la scadenza dei dati tramite eliminazione in base al tempo trascorso. Nell'esempio seguente illustra un criterio che consente di eliminare tutti i BLOB in blocchi più vecchi di 365 giorni.

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

### <a name="delete-old-snapshots"></a>Eliminare gli snapshot precedenti

Per i dati che vengono modificati e usati regolarmente per tutto il loro ciclo di vita vengono spesso usati gli snapshot per tenere traccia delle versioni precedenti dei dati. È possibile creare criteri che eliminino gli snapshot precedenti in base al tempo trascorso. Il tempo trascorso per lo snapshot viene determinato valutando la sua data/ora di creazione. Questa regola dei criteri elimina gli snapshot dei BLOB in blocchi all'interno del contenitore `activedata` per i quali sono trascorsi 90 giorni o più dalla creazione.

```json
{
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="faq"></a>Domande frequenti

**Ho creato un nuovo criterio, perché le azioni non vengono eseguite immediatamente?**  
La piattaforma esegue i criteri di gestione del ciclo di vita una volta al giorno. Dopo aver configurato un criterio, può richiedere fino a 24 ore per alcune azioni da eseguire per la prima volta.  

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come recuperare i dati dopo l'eliminazione accidentale:

- [Eliminazione temporanea per i BLOB in archiviazione di Azure](../blobs/storage-blob-soft-delete.md)
