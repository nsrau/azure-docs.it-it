---
title: Gestione del ciclo di vita di Archiviazione di Azure
description: Informazioni su come creare regole dei criteri del ciclo di vita per la transizione dei dati da livelli di archiviazione ad accesso frequente a livelli di archiviazione ad accesso sporadico e archivio.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: article
ms.date: 11/04/2018
ms.author: yzheng
ms.component: common
ms.openlocfilehash: 5c77d7d8f1ce3b4a13e497d461244aae5b34d08c
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53631363"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Gestione del ciclo di vita di Archiviazione BLOB di Azure (anteprima)

I set di dati hanno cicli di vita specifici. All'inizio del ciclo di vita, gli utenti accedono ad alcuni dati spesso. La necessità di accesso si riduce tuttavia drasticamente con l'invecchiamento dei dati. Alcuni dati rimangono inattivi nel cloud e gli utenti vi accedono raramente dopo l'archiviazione. Alcuni dati scadono giorni o mesi dopo la creazione, mentre altri set di dati vengono letti e modificati per l'intero ciclo di vita. La gestione del ciclo di vita di Archiviazione BLOB di Azure (anteprima) offre criteri avanzati e basati su regole per gli account di archiviazione GPv2 e BLOB. Usare i criteri per trasferire i dati ai livelli di accesso appropriati o farli scadere alla fine del loro ciclo di vita.

I criteri di gestione del ciclo di vita consentono di eseguire queste operazioni:

- Spostare i BLOB a un livello di archiviazione ad accesso più sporadico (da frequente a sporadico, da frequente ad archivio o da sporadico ad archivio) per ottimizzare costi e prestazioni
- Eliminare i BLOB al termine del ciclo di vita
- Definire le regole da eseguire una volta al giorno a livello di account di archiviazione
- Applicare regole ai contenitori o a un sottoinsieme di BLOB (usando i prefissi come filtri)

Si consideri lo scenario in cui un set di dati ha un accesso frequente durante le prime fasi del ciclo di vita, ma solo occasionale dopo due settimane. Oltre il primo mese, l'accesso al set di dati è raro. In questo scenario è consigliabile l'archiviazione ad accesso frequente durante le fasi iniziali. Il livello di archiviazione ad accesso sporadico è quello più appropriato per l'accesso occasionale e il livello di archiviazione archivio è l'opzione migliore dopo che è trascorso più di un mese. Grazie alla regolazione dei livelli di archiviazione in base alla validità dei dati, è possibile progettare le opzioni di archiviazione meno costose per le proprie esigenze. Per realizzare questa transizione, sono disponibili regole dei criteri di gestione del ciclo di vita per spostare i dati a livelli di archiviazione ad accesso più sporadico.

## <a name="storage-account-support"></a>Supporto dell'account di archiviazione

I criteri di gestione del ciclo di vita sono disponibili sia con gli account per utilizzo generico v2 (GPv2), sia con gli account di archiviazione BLOB. Nel portale di Azure è possibile aggiornare un account per utilizzo generico (GPv1) esistente in un account GPv2 con un semplice clic. Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).  

## <a name="pricing"></a>Prezzi 

La funzionalità di gestione del ciclo di vita è gratuita in anteprima. Ai clienti viene addebitato il normale costo dell'operazione per le chiamate API [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) (Elenca BLOB) e [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) (Imposta livello BLOB). Per altre informazioni sui prezzi, vedere [Prezzi dei BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="register-for-preview"></a>Eseguire la registrazione per l'anteprima 
Per registrarsi per l'anteprima pubblica è necessario inviare una richiesta per registrare questa funzionalità nella propria sottoscrizione. Le richieste vengono generalmente approvate entro due settimane. Dopo l'approvazione, tutti gli account di archiviazione GPv2 o BLOB esistenti e nuovi nelle aree seguenti includono la funzionalità: Stati Uniti occidentali 2, Stati Uniti centro-occidentali, Stati Uniti orientali 2 ed Europa occidentale. L'anteprima supporta solo il BLOB in blocchi. Come per la maggior parte delle anteprime, non usare questa funzione per i carichi di lavoro di produzione fino al momento della disponibilità generale.

Per inviare una richiesta, eseguire i comandi PowerShell o dell'interfaccia della riga di comando seguenti.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per inviare una richiesta:

```powershell
Register-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
È possibile controllare lo stato di approvazione della registrazione con il comando seguente:
```powershell
Get-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
Con l'approvazione e la registrazione corretta, si ottiene lo stato *Registrato* quando si inoltrano le richieste precedenti.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per inviare una richiesta: 
```cli
az feature register --namespace Microsoft.Storage --name DLM
```
È possibile controllare lo stato di approvazione della registrazione con il comando seguente:
```cli
az feature show --namespace Microsoft.Storage --name DLM
```
Con l'approvazione e la registrazione corretta, si ottiene lo stato *Registrato* quando si inoltrano le richieste precedenti.


## <a name="add-or-remove-a-policy"></a>Aggiungere o rimuovere un criterio 

È possibile aggiungere, modificare o rimuovere criteri tramite il portale di Azure, [PowerShell](https://www.powershellgallery.com/packages/Az.Storage), l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ext/storage-preview/storage/account/management-policy?view=azure-cli-latest#ext-storage-preview-az-storage-account-management-policy-create), le [API REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies/createorupdate) o gli strumenti client nei linguaggi seguenti: .[NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby](https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **Tutte le risorse** e quindi l'account di archiviazione.

3. Selezionare **Gestione del ciclo di vita (anteprima)** in Servizio BLOB per visualizzare o modificare i criteri.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```
az account set --subscription "[subscriptionName]”
az extension add --name storage-preview
az storage account management-policy show --resource-group [resourceGroupName] --account-name [accountName]
```

> [!NOTE]
Se per l'account di archiviazione si abilitano regole firewall, è possibile che le richieste di gestione del ciclo di vita vengano bloccate. È possibile sbloccare queste richieste specificando eccezioni. Per altre informazioni, vedere la sezione Eccezioni in [Configurare i firewall e le reti virtuali](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policy"></a>Criterio

I criteri di gestione del ciclo di vita sono una raccolta di regole in un documento JSON:

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "rule1",
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


Un criterio richiede due parametri:

| Nome parametro | Tipo di parametro | Note |
|----------------|----------------|-------|
| version        | Una stringa espressa come `x.x` | Il numero della versione di anteprima è 0.5. |
| regole          | Una matrice di oggetti regola | È necessaria almeno una regola in ogni criterio. Durante l'anteprima è possibile specificare fino a 4 regole per criterio. |

Ogni regola all'interno del criterio richiede tre parametri:

| Nome parametro | Tipo di parametro | Note |
|----------------|----------------|-------|
| NOME           | string | Il nome di una regola può contenere qualsiasi combinazione di caratteri alfanumerici. Nel nome della regola viene applicata la distinzione tra maiuscole e minuscole. Il nome deve essere univoco nel criterio. |
| type           | Un valore di enumerazione | Il valore valido per l'anteprima è `Lifecycle`. |
| Definizione     | Un oggetto che definisce la regola del ciclo di vita | Ogni definizione è composta da un set di filtri e un set di azioni. |

## <a name="rules"></a>Regole

La definizione di ogni regola include un set di filtri e un set di azioni. Il [set di filtri](#rule-filters) limita le azioni della regola a un determinato set di oggetti all'interno di un contenitore o di nomi di oggetti. Il [set di azioni](#rule-actions) applica le azioni tier o delete al set filtrato di oggetti.

### <a name="sample-rule"></a>Regola di esempio
La regola di esempio seguente filtra l'account per l'esecuzione di azioni solo in `container1/foo`. Eseguire queste azioni per tutti gli oggetti che si trovano all'interno di `container1` **E** iniziano con `foo`: 

- Impostare il BLOB sul livello di accesso sporadico 30 giorni dopo l'ultima modifica
- Impostare il BLOB sul livello archivio 90 giorni dopo l'ultima modifica
- Eliminare il BLOB 2.555 giorni (7 anni) dopo l'ultima modifica
- Elimina gli snapshot del BLOB 90 giorni dopo la creazione dello snapshot

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "ruleFoo", 
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

Durante l'anteprima, i filtri validi includono:

| Nome filtro | Tipo di filtro | Note | Obbligatorio |
|-------------|-------------|-------|-------------|
| blobTypes   | Una matrice di valori di enumerazione predefiniti. | La versione di anteprima supporta solo `blockBlob`. | Yes |
| prefixMatch | Una matrice di stringhe per i prefissi corrispondenti. Una stringa di prefisso deve iniziare con un nome di contenitore. Se ad esempio si vogliono cercare tutti i BLOB sotto "https://myaccount.blob.core.windows.net/container1/foo/..." per una regola, prefixMatch è `container1/foo`. | Se prefixMatch non è definito, le regole si applicano a tutti i BLOB all'interno dell'account. | No  |

### <a name="rule-actions"></a>Azioni della regola

Le azioni vengono applicate ai BLOB filtrati quando viene soddisfatta la condizione di esecuzione.

Nella versione di anteprima la gestione del ciclo di vita supporta la suddivisione in livelli, l'eliminazione dei BLOB e l'eliminazione degli snapshot dei BLOB. Definire almeno un'azione per ogni regola sui BLOB o sugli snapshot dei BLOB.

| Azione        | BLOB di base                                   | Snapshot      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Supporta i BLOB attualmente al livello di archiviazione ad accesso frequente         | Non supportate |
| tierToArchive | Supporta i BLOB attualmente al livello di archiviazione ad accesso frequente o sporadico | Non supportate |
| delete        | Supportato                                   | Supportato     |

>[!NOTE] 
Se nello stesso BLOB è stata definita più di un'azione, la gestione del ciclo di vita applica al BLOB l'azione meno costosa. Ad esempio, l'azione `delete` è meno costosa dell'azione `tierToArchive`. L'azione `tierToArchive` è meno costosa dell'azione `tierToCool`.

Nell'anteprima le condizioni di esecuzione dell'azione si basano sul tempo trascorso. Per tenere traccia del tempo trascorso, i BLOB di base usano la data/ora dell'ultima modifica, mentre gli snapshot dei BLOB usano la data/ora di creazione dello snapshot.

| Condizione di esecuzione dell'azione | Valore della condizione | DESCRIZIONE |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Valore intero che indica il tempo trascorso in giorni | Condizione valida per le azioni del BLOB di base |
| daysAfterCreationGreaterThan     | Valore intero che indica il tempo trascorso in giorni | Condizione valida per le azioni dello snapshot del BLOB | 

## <a name="examples"></a>Esempi
Gli esempi seguenti illustrano come affrontare scenari comuni relativi alle regole dei criteri del ciclo di vita.

### <a name="move-aging-data-to-a-cooler-tier"></a>Spostare i dati a un livello di archiviazione ad accesso più sporadico

Questo esempio illustra la transizione di BLOB in blocchi con prefisso `container1/foo` o `container2/bar`. Il criterio sposta i BLOB che non sono stati modificati da oltre 30 giorni a un livello di archiviazione ad accesso sporadico e i BLOB non modificati da 90 giorni a un livello di archiviazione archivio:

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "agingRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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

Alcuni dati rimangono inattivi sul cloud e gli utenti vi accedono raramente, se non mai, dopo l'archiviazione. Archiviare questi dati subito dopo l'inserimento. Per archiviare i dati al momento dell'inserimento viene configurato il criterio del ciclo di vita seguente. In questo esempio i BLOB in blocchi nell'account di archiviazione all'interno del contenitore `archivecontainer` vengono immediatamente spostati in un livello di archiviazione. La transizione immediata avviene agendo sui BLOB 0 giorni dopo la data dell'ultima modifica:

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "archiveRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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

Per alcuni dati è prevista una scadenza di un determinato numero di giorni o mesi dopo la creazione per ridurre i costi o soddisfare i requisiti di legge. È possibile configurare un criterio di gestione del ciclo di vita per impostare la scadenza dei dati tramite eliminazione in base al tempo trascorso. L'esempio seguente illustra un criterio che elimina tutti i BLOB in blocchi (senza alcun prefisso specificato) dopo che sono trascorsi 365 giorni.

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "expirationRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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
  "version": "0.5",
  "rules": [ 
    {
      "name": "snapshotRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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
## <a name="faq---i-created-a-new-policy-why-are-the-actions-not-run-immediately"></a>Domanda frequente: ho creato un nuovo criterio, perché le azioni non vengono eseguite immediatamente? 

La piattaforma esegue i criteri di gestione del ciclo di vita una volta al giorno. Dopo aver impostato un nuovo criterio, possono essere necessarie fino a 24 ore per l'avvio e l'esecuzione di alcune azioni, come la suddivisione in livelli e l'eliminazione.  

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come recuperare i dati dopo l'eliminazione accidentale:

- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](../blobs/storage-blob-soft-delete.md)
