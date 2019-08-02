---
title: Gestione del ciclo di vita di archiviazione di Azure
description: Informazioni su come creare regole dei criteri del ciclo di vita per la transizione dei dati da livelli di archiviazione ad accesso frequente a livelli di archiviazione ad accesso sporadico e archivio.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: common
ms.openlocfilehash: cd02051c0ef1dfe93b1ee67a0a9605e1611f336b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565981"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Gestire il ciclo di vita dell'archiviazione BLOB di Azure

I set di dati hanno cicli di vita specifici. All'inizio del ciclo di vita, gli utenti accedono ad alcuni dati spesso. La necessità di accesso si riduce tuttavia drasticamente con l'invecchiamento dei dati. Alcuni dati rimangono inattivi nel cloud e gli utenti vi accedono raramente dopo l'archiviazione. Alcuni dati scadono giorni o mesi dopo la creazione, mentre altri set di dati vengono letti e modificati per l'intero ciclo di vita. Gestione del ciclo di vita dell'archiviazione BLOB di Azure offre un criterio completo basato su regole per gli account di archiviazione BLOB e GPv2. Usare i criteri per trasferire i dati ai livelli di accesso appropriati o farli scadere alla fine del loro ciclo di vita.

I criteri di gestione del ciclo di vita consentono di eseguire queste operazioni:

- Spostare i BLOB a un livello di archiviazione ad accesso più sporadico (da frequente a sporadico, da frequente ad archivio o da sporadico ad archivio) per ottimizzare costi e prestazioni
- Eliminare i BLOB al termine del ciclo di vita
- Definire le regole da eseguire una volta al giorno a livello di account di archiviazione
- Applicare regole ai contenitori o a un sottoinsieme di BLOB (usando i prefissi come filtri)

Si consideri uno scenario in cui i dati ottengono un accesso frequente durante le fasi iniziali del ciclo di vita, ma solo occasionalmente dopo due settimane. Oltre il primo mese, l'accesso al set di dati è raro. In questo scenario è consigliabile l'archiviazione ad accesso frequente durante le fasi iniziali. L'archiviazione ad accesso sporadico è più appropriata per l'accesso occasionale. Storage Archive è l'opzione del livello migliore dopo le età dei dati in un mese. Grazie alla regolazione dei livelli di archiviazione in base alla validità dei dati, è possibile progettare le opzioni di archiviazione meno costose per le proprie esigenze. Per realizzare questa transizione, sono disponibili regole dei criteri di gestione del ciclo di vita per spostare i dati a livelli di archiviazione ad accesso più sporadico.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>Supporto dell'account di archiviazione

Il criterio di gestione del ciclo di vita è disponibile con gli account per utilizzo generico V2 (GPv2), gli account di archiviazione BLOB e gli account di archiviazione BLOB in blocchi Premium. Nella portale di Azure è possibile aggiornare un account di per utilizzo generico esistente (utilizzo generico V1) a un account GPv2. Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).  

## <a name="pricing"></a>Prezzi

La funzionalità di gestione del ciclo di vita è gratuita. Ai clienti viene addebitato il normale costo dell'operazione per le chiamate API [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) (Elenca BLOB) e [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) (Imposta livello BLOB). L'operazione di eliminazione è gratuita. Per altre informazioni sui prezzi, vedere [Prezzi dei BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Disponibilità a livello di area

La funzionalità di gestione del ciclo di vita è disponibile in tutte le aree di Azure.

## <a name="add-or-remove-a-policy"></a>Aggiungere o rimuovere un criterio

È possibile aggiungere, modificare o rimuovere un criterio utilizzando uno dei metodi seguenti:

* [Portale di Azure](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [API REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Questo articolo illustra come gestire i criteri usando il portale e i metodi di PowerShell.  

> [!NOTE]
> Se per l'account di archiviazione si abilitano regole firewall, è possibile che le richieste di gestione del ciclo di vita vengano bloccate. È possibile sbloccare queste richieste specificando eccezioni. Il bypass necessario è: `Logging,  Metrics,  AzureServices`. Per altre informazioni, vedere la sezione Eccezioni in [Configurare i firewall e le reti virtuali](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

### <a name="azure-portal"></a>Portale di Azure

Esistono due modi per aggiungere un criterio tramite il portale di Azure. 

* [Visualizzazione elenco portale di Azure](#azure-portal-list-view)
* [Visualizzazione codice portale di Azure](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Visualizzazione elenco portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **tutte le risorse** e quindi selezionare l'account di archiviazione.

3. In **servizio BLOB**selezionare **gestione del ciclo** di vita per visualizzare o modificare le regole.

4. Selezionare la scheda **visualizzazione elenco** .

5. Selezionare **Aggiungi regola** e quindi compilare i campi del modulo del **set di azioni** . Nell'esempio seguente i BLOB vengono spostati nell'archiviazione ad accesso sporadico se non sono stati modificati per 30 giorni.

   ![Pagina set di azioni di gestione del ciclo di vita in portale di Azure](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. Selezionare **filtro impostato** per aggiungere un filtro facoltativo. Quindi selezionare **Sfoglia** per specificare un contenitore e una cartella in base ai quali filtrare.

   ![Pagina set di filtri di gestione del ciclo di vita in portale di Azure](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. Selezionare **Verifica + Aggiungi** per esaminare le impostazioni dei criteri.

9. Selezionare **Aggiungi** per aggiungere il nuovo criterio.

#### <a name="azure-portal-code-view"></a>Visualizzazione codice portale di Azure
1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **tutte le risorse** e quindi selezionare l'account di archiviazione.

3. In **servizio BLOB**selezionare **gestione del ciclo** di vita per visualizzare o modificare i criteri.

4. Il codice JSON seguente è un esempio di criteri che possono essere incollati nella scheda **visualizzazione codice** .

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

5. Selezionare **Salva**.

6. Per altre informazioni su questo esempio JSON, vedere le sezioni [criteri](#policy) e [regole](#rules) .

### <a name="powershell"></a>PowerShell

Lo script di PowerShell seguente può essere usato per aggiungere un criterio all'account di archiviazione. La `$rgname` variabile deve essere inizializzata con il nome del gruppo di risorse. La `$accountName` variabile deve essere inizializzata con il nome dell'account di archiviazione.

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

| Nome parametro | Tipo di parametro | Note | Obbligatoria |
|----------------|----------------|-------|----------|
| `name`         | String |Il nome di una regola può includere fino a 256 caratteri alfanumerici. Nel nome della regola viene applicata la distinzione tra maiuscole e minuscole.  Il nome deve essere univoco nel criterio. | True |
| `enabled`      | Boolean | Valore booleano facoltativo per consentire la disabilitazione temporanea di una regola. Il valore predefinito è true se non è impostato. | False | 
| `type`         | Un valore di enumerazione | Il tipo valido corrente è `Lifecycle`. | True |
| `definition`   | Un oggetto che definisce la regola del ciclo di vita | Ogni definizione è composta da un set di filtri e un set di azioni. | True |

## <a name="rules"></a>Regole

La definizione di ogni regola include un set di filtri e un set di azioni. Il [set di filtri](#rule-filters) limita le azioni della regola a un determinato set di oggetti all'interno di un contenitore o di nomi di oggetti. Il [set di azioni](#rule-actions) applica le azioni tier o delete al set filtrato di oggetti.

### <a name="sample-rule"></a>Regola di esempio

La regola di esempio seguente Filtra l'account per eseguire le azioni sugli oggetti presenti all' `container1` interno di e `foo`iniziare con.  

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
| blobTypes   | Una matrice di valori di enumerazione predefiniti. | La versione corrente supporta `blockBlob`. | Yes |
| prefixMatch | Una matrice di stringhe per i prefissi corrispondenti. Ogni regola può definire un massimo di 10 prefissi. Una stringa di prefisso deve iniziare con un nome di contenitore. Se ad esempio si desidera trovare la corrispondenza di tutti i `https://myaccount.blob.core.windows.net/container1/foo/...` BLOB in per una regola, prefixMatch `container1/foo`è. | Se non si definisce prefixMatch, la regola si applica a tutti i BLOB all'interno dell'account di archiviazione.  | No |

### <a name="rule-actions"></a>Azioni regola

Le azioni vengono applicate ai BLOB filtrati quando viene soddisfatta la condizione di esecuzione.

La gestione del ciclo di vita supporta la suddivisione in livelli e l'eliminazione di BLOB e l'eliminazione di snapshot BLOB. Definire almeno un'azione per ogni regola sui BLOB o sugli snapshot dei BLOB.

| Azione        | BLOB di base                                   | Snapshot      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Supporta i BLOB attualmente al livello di archiviazione ad accesso frequente         | Non supportate |
| tierToArchive | Supporta i BLOB attualmente al livello di archiviazione ad accesso frequente o sporadico | Non supportate |
| delete        | Supportato                                   | Supportato     |

>[!NOTE]
>Se nello stesso BLOB è stata definita più di un'azione, la gestione del ciclo di vita applica al BLOB l'azione meno costosa. Ad esempio, l'azione `delete` è meno costosa dell'azione `tierToArchive`. L'azione `tierToArchive` è meno costosa dell'azione `tierToCool`.

Le condizioni di esecuzione sono basate sull'età. Per tenere traccia del tempo trascorso, i BLOB di base usano la data/ora dell'ultima modifica, mentre gli snapshot dei BLOB usano la data/ora di creazione dello snapshot.

| Condizione di esecuzione azione             | Valore della condizione                          | Descrizione                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | Valore intero che indica il tempo trascorso in giorni | Condizione per le azioni BLOB di base     |
| daysAfterCreationGreaterThan     | Valore intero che indica il tempo trascorso in giorni | Condizione per le azioni snapshot BLOB |

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

Alcuni dati rimangono inattivi sul cloud e gli utenti vi accedono raramente, se non mai, dopo l'archiviazione. I criteri del ciclo di vita seguenti sono configurati per archiviare i dati dopo l'inserimento. Questo esempio esegue la transizione dei BLOB in blocchi nell'account di `archivecontainer` archiviazione all'interno del contenitore in un livello archivio. La transizione viene eseguita agendo sui BLOB 0 giorni dopo l'ora dell'Ultima modifica:

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
La piattaforma esegue i criteri di gestione del ciclo di vita una volta al giorno. Una volta configurati i criteri, possono essere necessarie fino a 24 ore prima che alcune azioni vengano eseguite per la prima volta.  

**Riattivare manualmente un BLOB archiviato. come è possibile impedire che venga spostato di nuovo nel livello di archiviazione temporaneamente?**  
Quando un BLOB viene spostato da un livello di accesso a un altro, l'ora dell'Ultima modifica non cambia. Se si riattiva manualmente un BLOB archiviato in un livello di accesso frequente, questo verrà spostato di nuovo al livello archivio dal motore di gestione del ciclo di vita. Disabilitare temporaneamente la regola che influisca su questo BLOB per impedirne l'archiviazione. Copiare il BLOB in un altro percorso se è necessario mantenerlo in modo permanente. Abilitare nuovamente la regola quando il BLOB può essere spostato di nuovo in modo sicuro nel livello archivio. 


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come recuperare i dati dopo l'eliminazione accidentale:

- [Eliminazione temporanea per i BLOB di archiviazione di Azure](../blobs/storage-blob-soft-delete.md)
