---
title: Gestione del ciclo di vita di Archiviazione di Azure
description: Informazioni su come creare regole dei criteri del ciclo di vita per la transizione dei dati da livelli di archiviazione ad accesso frequente a livelli di accesso sporadico e archivio.
services: storage
author: yzheng-msft
manager: jwillis
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 04/30/2018
ms.author: yzheng
ms.openlocfilehash: ec314925635d34baa7b3edeeb397805964b6353d
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413128"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Gestione del ciclo di vita di Archiviazione BLOB di Azure (anteprima)

I set di dati hanno cicli di vita specifici. Ad alcuni dati si accede spesso all'inizio del ciclo di vita, ma la necessità di accedere diminuisce notevolmente con il trascorrere del tempo. Alcuni dati rimangono inattivi nel cloud e gli utenti vi accedono raramente dopo l'archiviazione. Alcuni dati scadono giorni o mesi dopo la creazione, mentre altri set di dati vengono letti e modificati per l'intero ciclo di vita. La gestione del ciclo di vita di Archiviazione BLOB di Azure (anteprima) offre criteri avanzati basati su regole che è possibile usare per trasferire i dati al livello di accesso più appropriato e definirne la scadenza al termine del relativo ciclo di vita.

I criteri di gestione del ciclo di vita consentono di:

- Spostare i BLOB a un livello di archiviazione ad accesso più sporadico (da frequente a sporadico, da frequente ad archivio o da sporadico ad archivio) per ottimizzare costi e prestazioni
- Eliminare i BLOB al termine del ciclo di vita
- Definire le regole da eseguire una volta al giorno a livello di account di archiviazione (sono supportati account di archiviazione GPv2 e BLOB)
- Applicare regole ai contenitori o a un sottoinsieme di BLOB (usando i prefissi come filtri)

Si consideri un set di dati a cui si accede di frequente durante la fase iniziale del ciclo di vita, solo occasionalmente dopo due settimane e raramente dopo un mese e oltre. In questo scenario il livello di archiviazione ad accesso frequente è la scelta migliore durante le fasi iniziali, il livello di archiviazione ad accesso sporadico è quello più appropriato per l'accesso occasionale e il livello di archiviazione archivio è l'opzione migliore dopo che è trascorso più di un mese. Grazie alla regolazione dei livelli di archiviazione in base alla validità dei dati, è possibile progettare le opzioni di archiviazione meno costose per le proprie esigenze. Per realizzare questa transizione sono disponibili criteri di gestione del ciclo di vita per spostare i dati a livelli di archiviazione ad accesso più sporadico.

## <a name="storage-account-support"></a>Supporto dell'account di archiviazione

I criteri di gestione del ciclo di vita sono disponibili sia con l'account per utilizzo generico v2 (GPv2) sia con l'account di archiviazione BLOB. Nel portale di Azure è possibile convertire un account per utilizzo generico (GPv1) esistente in un account GPv2 con un semplice clic. Per altre informazioni, vedere [Opzioni di account di archiviazione di Azure](../common/storage-account-options.md).  

## <a name="pricing"></a>Prezzi 

La funzionalità di gestione del ciclo di vita è gratuita in anteprima. Ai clienti viene addebitato il normale costo dell'operazione per le chiamate API [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) (Elenca BLOB) e [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) (Imposta livello BLOB). Vedere [Prezzi per i BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/) per altre informazioni sui prezzi.

## <a name="register-for-preview"></a>Eseguire la registrazione per l'anteprima 
Per registrarsi nell'anteprima pubblica è necessario inviare una richiesta per registrare questa funzionalità nella propria sottoscrizione. Dopo che la richiesta è stata approvata (entro pochi giorni), gli account GPv2 o di Archiviazione BLOB nuovi ed esistenti nelle aree Stati Uniti occidentali 2 e Stati Uniti centro-occidentali avranno la funzionalità abilitata. Durante l'anteprima è supportato solo il BLOB in blocchi. Come per la maggior parte delle versioni di anteprima, questa funzionalità non deve essere usata per i carichi di lavoro in produzione fino al momento della disponibilità generale.

Per inviare una richiesta, eseguire i comandi PowerShell o dell'interfaccia della riga di comando seguenti.

### <a name="powershell"></a>PowerShell

Per inviare una richiesta:

```powershell
Register-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
È possibile controllare lo stato di approvazione della registrazione con il comando seguente:
```powershell
Get-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
Se la funzionalità è stata approvata e registrata correttamente, si dovrebbe ricevere lo stato di "Registrazione completata".

### <a name="cli-20"></a>Interfaccia della riga di comando 2.0

Per inviare una richiesta: 
```cli
az feature register –-namespace Microsoft.Storage –-name DLM
```
È possibile controllare lo stato di approvazione della registrazione con il comando seguente:
```cli
-az feature show –-namespace Microsoft.Storage –-name DLM
```
Se la funzionalità è stata approvata e registrata correttamente, si dovrebbe ricevere lo stato di "Registrazione completata". 


## <a name="add-or-remove-policies"></a>Aggiungere o rimuovere criteri 

È possibile aggiungere, modificare o rimuovere criteri tramite il portale di Azure, [PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.3-preview), le [API REST](https://docs.microsoft.com/en-us/rest/api/storagerp/storageaccounts/createorupdatemanagementpolicies) o gli strumenti client nei linguaggi seguenti: [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby]( https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Per passare all'account di archiviazione, selezionare Tutte le risorse, quindi selezionare l'account di archiviazione.

3. Nel pannello Impostazioni fare clic su **Gestione del ciclo di vita** raggruppato in Servizio BLOB per visualizzare e/o modificare i criteri.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

> [!NOTE]
Se per l'account di archiviazione si abilitano regole firewall, è possibile che le richieste di gestione del ciclo di vita vengano bloccate. Per sbloccarle, specificare eccezioni. Per altre informazioni, vedere la sezione Eccezioni in [Configurare i firewall e le reti virtuali](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policies"></a>Criteri

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


All'interno di un criterio sono necessari due parametri:

| Nome parametro | Tipo di parametro | Note |
|----------------|----------------|-------|
| version        | Una stringa espressa come `x.x` | Il numero della versione di anteprima è 0.5 |
| regole          | Una matrice di oggetti regola | È necessaria almeno una regola in ogni criterio. Durante l'anteprima è possibile specificare fino a 4 regole per criterio. |

I parametri necessari all'interno di una regola sono:

| Nome parametro | Tipo di parametro | Note |
|----------------|----------------|-------|
| NOME           | string | Il nome di una regola può contenere qualsiasi combinazione di caratteri alfanumerici. Nel nome della regola viene applicata la distinzione tra maiuscole e minuscole. Il nome deve essere univoco nel criterio. |
| type           | Un valore di enumerazione | Il valore valido per l'anteprima è `Lifecycle` |
| Definizione     | Un oggetto che definisce la regola del ciclo di vita | Ogni definizione è costituita da un set di filtri e un set di azioni. |

## <a name="rules"></a>Regole

La definizione di ogni regola include un set di filtri e un set di azioni. La regola di esempio seguente modifica il livello per i BLOB in blocchi di base con il prefisso `container1/foo`. Nel criterio queste regole sono definite nel modo seguente:

- Imposta il BLOB sul livello di archiviazione ad accesso sporadico 30 giorni dopo l'ultima modifica
- Imposta il BLOB sul livello di archiviazione archivio 90 giorni dopo l'ultima modifica
- Elimina il BLOB 2.555 giorni (7 anni) dopo l'ultima modifica
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

I filtri limitano le azioni della regola a un sottoinsieme di BLOB all'interno dell'account di archiviazione. Se sono stati definiti più filtri, viene eseguita un'operazione logica `AND` su tutti i filtri.

Durante l'anteprima, i filtri validi includono:

| Nome filtro | Tipo di filtro | Note | Obbligatorio |
|-------------|-------------|-------|-------------|
| blobTypes   | Una matrice di valori di enumerazione predefiniti. | Per la versione di anteprima è supportato solo `blockBlob`. | Yes |
| prefixMatch | Una matrice di stringhe per i prefissi corrispondenti. Una stringa di prefisso deve iniziare con un nome di contenitore. Ad esempio, se tutti i BLOB in "https://myaccount.blob.core.windows.net/mycontainer/mydir/..." devono essere abbinati per una regola, il prefisso è "mycontainer/mydir". | Se non è definita, questa regola si applica a tutti i BLOB all'interno dell'account. | No  |

### <a name="rule-actions"></a>Azioni della regola

Le azioni vengono applicate ai BLOB filtrati quando viene soddisfatta la condizione di esecuzione.

Nella versione di anteprima la gestione del ciclo di vita supporta la suddivisione in livelli e l'eliminazione del BLOB e l'eliminazione degli snapshot del BLOB. Ogni regola deve avere almeno un'azione definita sui BLOB o sugli snapshot dei BLOB.

| Azione        | BLOB di base                                   | Snapshot      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Supporta i BLOB attualmente al livello di archiviazione ad accesso frequente         | Non supportate |
| tierToArchive | Supporta i BLOB attualmente al livello di archiviazione ad accesso frequente o sporadico | Non supportate |
| delete        | Supportato                                   | Supportato     |

>[!NOTE] 
Se nello stesso BLOB è stata definita più di un'azione, la gestione del ciclo di vita applica al BLOB l'azione meno costosa. Ad esempio, l'azione `delete` è meno costosa dell'azione `tierToArchive`. L'azione `tierToArchive` è meno costosa dell'azione `tierToCool`.

Nell'anteprima le condizioni di esecuzione dell'azione si basano sul tempo trascorso. Per tenere traccia del tempo trascorso, il BLOB di base usa la data/ora dell'ultima modifica, mentre gli snapshot del BLOB usano la data/ora di creazione dello snapshot.

| Condizione di esecuzione dell'azione | Valore della condizione | DESCRIZIONE |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Valore intero che indica il tempo trascorso in giorni | Condizione valida per le azioni del BLOB di base |
| daysAfterCreationGreaterThan     | Valore intero che indica il tempo trascorso in giorni | Condizione valida per le azioni dello snapshot del BLOB | 

## <a name="examples"></a>Esempi
Gli esempi seguenti illustrano come affrontare scenari comuni relativi alle regole dei criteri del ciclo di vita.

### <a name="move-aging-data-to-a-cooler-tier"></a>Spostare i dati a un livello di archiviazione ad accesso più sporadico

L'esempio seguente illustra la transizione di BLOB in blocchi con prefisso `container1/foo` o `container2/bar`. Il criterio sposta i BLOB che non sono stati modificati da oltre 30 giorni a un livello di archiviazione ad accesso sporadico e i BLOB non modificati da 90 giorni a un livello di archiviazione archivio:

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

Alcuni dati rimangono inattivi sul cloud e gli utenti vi accedono raramente, se non mai, dopo l'archiviazione. È consigliabile archiviare questi dati subito dopo l'inserimento. Per archiviare i dati al momento dell'inserimento viene configurato il criterio del ciclo di vita seguente. In questo esempio i BLOB in blocchi nell'account di archiviazione all'interno del contenitore `archivecontainer` vengono immediatamente spostati in un livello di archiviazione. La transizione immediata avviene agendo sui BLOB 0 giorni dopo la data dell'ultima modifica:

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

Per alcuni dati è prevista di scadenza un determinato numero di giorni o mesi dopo la creazione per ridurre i costi o mantenere la conformità alle normative. È possibile configurare un criterio di gestione del ciclo di vita per impostare la scadenza dei dati tramite eliminazione in base al tempo trascorso. L'esempio seguente illustra un criterio che elimina tutti i BLOB in blocchi (senza alcun prefisso specificato) dopo che sono trascorsi 365 giorni.

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

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come recuperare i dati dopo l'eliminazione accidentale:

- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](../blobs/storage-blob-soft-delete.md)
