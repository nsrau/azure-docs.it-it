---
title: Gestione del ciclo di vita di Archiviazione di AzureManaging the Azure Storage lifecycle
description: Informazioni su come creare regole dei criteri del ciclo di vita per la transizione dei dati da livelli di archiviazione ad accesso frequente a livelli di archiviazione ad accesso sporadico e archivio.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: 238c12baf55b525a24107a727d09588ef06a6bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598307"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Gestire il ciclo di vita di Archiviazione BLOB di Azure

I set di dati hanno cicli di vita specifici. All'inizio del ciclo di vita, gli utenti accedono ad alcuni dati spesso. La necessità di accesso si riduce tuttavia drasticamente con l'invecchiamento dei dati. Alcuni dati rimangono inattivi nel cloud e gli utenti vi accedono raramente dopo l'archiviazione. Alcuni dati scadono giorni o mesi dopo la creazione, mentre altri set di dati vengono letti e modificati per l'intero ciclo di vita. La gestione del ciclo di vita dell'archiviazione BLOB di Azure offre criteri avanzati basati su regole per gli account GPv2 e di archiviazione BLOB. Usare i criteri per trasferire i dati ai livelli di accesso appropriati o farli scadere alla fine del loro ciclo di vita.

I criteri di gestione del ciclo di vita consentono di eseguire queste operazioni:

- Spostare i BLOB a un livello di archiviazione ad accesso più sporadico (da frequente a sporadico, da frequente ad archivio o da sporadico ad archivio) per ottimizzare costi e prestazioni
- Eliminare i BLOB al termine del ciclo di vita
- Definire le regole da eseguire una volta al giorno a livello di account di archiviazione
- Applicare regole ai contenitori o a un sottoinsieme di BLOB (usando i prefissi come filtri)

Si consideri uno scenario in cui i dati hanno accesso frequente durante le prime fasi del ciclo di vita, ma solo occasionalmente dopo due settimane. Oltre il primo mese, l'accesso al set di dati è raro. In questo scenario è consigliabile l'archiviazione ad accesso frequente durante le fasi iniziali. L'archiviazione raffreddata è più appropriata per l'accesso occasionale. L'archiviazione è l'opzione di livello migliore dopo l'invecchiamento dei dati nell'arco di un mese. Grazie alla regolazione dei livelli di archiviazione in base alla validità dei dati, è possibile progettare le opzioni di archiviazione meno costose per le proprie esigenze. Per realizzare questa transizione, sono disponibili regole dei criteri di gestione del ciclo di vita per spostare i dati a livelli di archiviazione ad accesso più sporadico.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>Supporto dell'account di archiviazione

I criteri di gestione del ciclo di vita sono disponibili con gli account GPv2 (General Purpose v2), gli account di archiviazione BLOB e gli account di archiviazione BLOB con blocchi Premium.The lifecycle management policy is available with General Purpose v2 (GPv2) accounts, Blob storage accounts, and Premium Block Blob storage accounts. Nel portale di Azure è possibile aggiornare un account GPv1 (General Purpose) esistente a un account GPv2. Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).  

## <a name="pricing"></a>Prezzi

La funzione di gestione del ciclo di vita è gratuita. Ai clienti viene addebitato il normale costo dell'operazione per le chiamate API [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) (Elenca BLOB) e [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) (Imposta livello BLOB). L'operazione di eliminazione è gratuita. Per altre informazioni sui prezzi, vedere [Prezzi dei BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Disponibilità a livello di area

La funzionalità di gestione del ciclo di vita è disponibile in tutte le aree di Azure.The lifecycle management feature is available in all Azure regions.

## <a name="add-or-remove-a-policy"></a>Aggiungere o rimuovere un criterio

È possibile aggiungere, modificare o rimuovere un criterio utilizzando uno dei metodi seguenti:

* [Portale di Azure](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Interfaccia della riga di comando di AzureAzure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [API REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Un criterio può essere letto o scritto per intero. Gli aggiornamenti parziali non sono supportati. 

> [!NOTE]
> Se per l'account di archiviazione si abilitano regole firewall, è possibile che le richieste di gestione del ciclo di vita vengano bloccate. È possibile sbloccare queste richieste fornendo eccezioni per i servizi Microsoft attendibili. Per altre informazioni, vedere la sezione Eccezioni in [Configurare i firewall e le reti virtuali](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

Questo articolo illustra come gestire i criteri usando il portale e i metodi di PowerShell.This article shows how to manage policy by using the portal and PowerShell methods.  

# <a name="portal"></a>[Portale](#tab/azure-portal)

Esistono due modi per aggiungere criteri tramite il portale di Azure.There are two ways to add a policy through the Azure portal. 

* [Visualizzazione elenco del portale di AzureAzure portal List view](#azure-portal-list-view)
* [Visualizzazione Codice del portale di AzureAzure portal Code view](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Visualizzazione elenco del portale di AzureAzure portal List view

1. Accedere al [portale](https://portal.azure.com)di Azure .

2. Nel portale di Azure cercare e selezionare l'account di archiviazione. 

3. In **Servizio BLOB**selezionare Gestione del **ciclo** di vita per visualizzare o modificare le regole.

4. Selezionare la scheda **Visualizzazione elenco.**

5. Selezionare **Aggiungi regola,** quindi compilare i campi del modulo **Set di** azioni. Nell'esempio seguente i BLOB vengono spostati nell'archiviazione ad archiviazione ad cool se non sono stati modificati per 30 giorni.

   ![Pagina del set di azioni per la gestione del ciclo di vita nel portale di AzureLifecycle management action set page in Azure portal](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. Selezionare **Set di filtri** per aggiungere un filtro facoltativo. Selezionare **quindi Sfoglia** per specificare un contenitore e una cartella in base ai quali filtrare.

   ![Pagina del set di filtri di gestione del ciclo di vita nel portale di AzureLifecycle management filter set page in Azure portal](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. Selezionare **Revisione : aggiungi** per rivedere le impostazioni dei criteri.

9. Selezionare **Aggiungi** per aggiungere il nuovo criterio.

#### <a name="azure-portal-code-view"></a>Visualizzazione Codice del portale di AzureAzure portal Code view
1. Accedere al [portale](https://portal.azure.com)di Azure .

2. Nel portale di Azure cercare e selezionare l'account di archiviazione.

3. In **Servizio BLOB**selezionare Gestione del **ciclo** di vita per visualizzare o modificare i criteri.

4. Il codice JSON seguente è un esempio di criterio che può essere incollato nella scheda **della vista Codice.The following** JSON is an example of a policy that can be pasted into the Code view tab.

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

6. Per altre informazioni su questo esempio JSON, vedere le sezioni [Criteri](#policy) e [regole.](#rules)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

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

# <a name="template"></a>[Modello](#tab/template)

È possibile definire la gestione del ciclo di vita usando i modelli di Azure Resource Manager.You can define lifecycle management by using Azure Resource Manager templates. Ecco un modello di esempio per distribuire un account di archiviazione RA-GRS GPv2 con criteri di gestione del ciclo di vita.

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

## <a name="policy"></a>Policy

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

Un criterio è una raccolta di regole:A policy is a collection of rules:

| Nome parametro | Tipo di parametro | Note |
|----------------|----------------|-------|
| `rules`        | Una matrice di oggetti regola | È necessaria almeno una regola in un criterio. È possibile definire fino a 100 regole in un criterio.|

Ogni regola all'interno del criterio ha diversi parametri:

| Nome parametro | Tipo di parametro | Note | Obbligatoria |
|----------------|----------------|-------|----------|
| `name`         | string |Il nome di una regola può includere fino a 256 caratteri alfanumerici. Nel nome della regola viene applicata la distinzione tra maiuscole e minuscole.  Il nome deve essere univoco nel criterio. | True |
| `enabled`      | Boolean | Valore booleano facoltativo per consentire la disabilitazione temporanea di una regola. Il valore predefinito è true se non è impostato. | False | 
| `type`         | Un valore di enumerazione | Il tipo valido `Lifecycle`corrente è . | True |
| `definition`   | Un oggetto che definisce la regola del ciclo di vita | Ogni definizione è composta da un set di filtri e un set di azioni. | True |

## <a name="rules"></a>Regole

La definizione di ogni regola include un set di filtri e un set di azioni. Il [set di filtri](#rule-filters) limita le azioni della regola a un determinato set di oggetti all'interno di un contenitore o di nomi di oggetti. Il [set di azioni](#rule-actions) applica le azioni tier o delete al set filtrato di oggetti.

### <a name="sample-rule"></a>Regola di esempio

La regola di esempio seguente filtra l'account `container1` per eseguire `foo`le azioni sugli oggetti presenti all'interno e che iniziano con .  

>[!NOTE]
>La gestione del ciclo di vita supporta solo il tipo di BLOB a blocchi.  

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

| Nome filtro | Tipo di filtro | Note | Obbligatorio |
|-------------|-------------|-------|-------------|
| blobTypes   | Una matrice di valori di enumerazione predefiniti. | La versione corrente `blockBlob`supporta . | Sì |
| prefixMatch | Una matrice di stringhe per i prefissi corrispondenti. Ogni regola può definire fino a 10 prefissi. Una stringa di prefisso deve iniziare con un nome di contenitore. Ad esempio, se si desidera trovare `https://myaccount.blob.core.windows.net/container1/foo/...` una corrispondenza con `container1/foo`tutti i BLOB in una regola, prefixMatch è . | Se non si definisce prefixMatch, la regola si applica a tutti i BLOB all'interno dell'account di archiviazione.  | No |

### <a name="rule-actions"></a>Azioni della regola

Le azioni vengono applicate ai BLOB filtrati quando viene soddisfatta la condizione di esecuzione.

La gestione del ciclo di vita supporta la suddivisione in livelli e l'eliminazione di BLOB e l'eliminazione di snapshot BLOB. Definire almeno un'azione per ogni regola sui BLOB o sugli snapshot dei BLOB.

| Azione        | BLOB di base                                   | Snapshot      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Supporta i BLOB attualmente al livello di archiviazione ad accesso frequente         | Non supportate |
| tierToArchive | Supporta i BLOB attualmente al livello di archiviazione ad accesso frequente o sporadico | Non supportate |
| delete        | Supportato                                   | Supportato     |

>[!NOTE]
>Se nello stesso BLOB è stata definita più di un'azione, la gestione del ciclo di vita applica al BLOB l'azione meno costosa. Ad esempio, l'azione `delete` è meno costosa dell'azione `tierToArchive`. L'azione `tierToArchive` è meno costosa dell'azione `tierToCool`.

Le condizioni di esecuzione si basano sull'età. Per tenere traccia del tempo trascorso, i BLOB di base usano la data/ora dell'ultima modifica, mentre gli snapshot dei BLOB usano la data/ora di creazione dello snapshot.

| Condizione di esecuzione dell'azione             | Valore della condizione                          | Descrizione                             |
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

### <a name="archive-data-after-ingest"></a>Archiviare i dati dopo l'inserimento

Alcuni dati rimangono inattivi sul cloud e gli utenti vi accedono raramente, se non mai, dopo l'archiviazione. I criteri del ciclo di vita seguenti sono configurati per archiviare i dati poco dopo l'inserimento. Questo esempio esegue la transizione dei BLOB `archivecontainer` di blocchi nell'account di archiviazione all'interno del contenitore in un livello di archiviazione. La transizione viene eseguita agendo sui BLOB 0 giorni dopo l'ora dell'ultima modifica:The transition is accomplished by acting on blobs 0 days after last modified time:

> [!NOTE] 
> È consigliabile caricare i BLOB direttamente dal livello di archiviazione per essere più efficienti. È possibile usare l'intestazione x-ms-acess-tier per [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) o [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) con la versione REST 2018-11-09 e versioni più recenti o le librerie client di archiviazione BLOB più recenti. 

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

Alcuni dati dovrebbero scadere giorni o mesi dopo la creazione. È possibile configurare un criterio di gestione del ciclo di vita per impostare la scadenza dei dati tramite eliminazione in base al tempo trascorso. L'esempio seguente mostra un criterio che elimina tutti i BLOB in blocchi anteriori a 365 giorni.

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
La piattaforma esegue i criteri di gestione del ciclo di vita una volta al giorno. Dopo aver configurato un criterio, l'esecuzione di alcune azioni per la prima volta può richiedere fino a 24 ore.  

**Se si aggiorna un criterio esistente, quanto tempo è necessario per l'esecuzione delle azioni?**  
L'entrata in vigore del criterio aggiornato richiede fino a 24 ore. Una volta che il criterio è attivo, potrebbero essere votizzate fino a 24 ore per l'esecuzione delle azioni. Pertanto, le azioni dei criteri possono richiedere fino a 48 ore per il completamento.   

**Ho reidratato manualmente un BLOB archiviato, come è possibile impedire che venga spostato temporaneamente al livello Archivio?**  
Quando un BLOB viene spostato da un livello di accesso a un altro, l'ora dell'ultima modifica non cambia. Se si reidrata manualmente un BLOB archiviato al livello intermedio, questo verrà spostato nuovamente nel livello di archiviazione dal motore di gestione del ciclo di vita. Disabilitare temporaneamente la regola che influisce temporaneamente su questo BLOB per impedirne l'archiviazione. Riattivare la regola quando il BLOB può essere spostato in modo sicuro al livello di archiviazione. È anche possibile copiare il BLOB in un'altra posizione se deve rimanere in un livello caldo o freddo in modo permanente.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come recuperare i dati dopo l'eliminazione accidentale:

- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](../blobs/storage-blob-soft-delete.md)
