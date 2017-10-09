---
title: Archiviare le credenziali in Azure Key Vault | Microsoft Docs
description: "Informazioni su come archiviare le credenziali per gli archivi dati usati in un insieme di credenziali delle chiavi di Azure che Azure Data Factory può recuperare automaticamente in fase di esecuzione."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fc8b4f1ecf664c7db5ab2e535245dee90415fa65
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="store-credential-in-azure-key-vault"></a>Archiviare le credenziali in Azure Key Vault

È possibile archiviare le credenziali per gli archivi dati in un [insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-whatis.md). Azure Data Factory Azure recupera le credenziali durante l'esecuzione di un'attività che usa l'archivio dati.

> [!NOTE]
> Attualmente solo il [connettore Dynamics](connector-dynamics-crm-office-365.md) supporta questa funzionalità. 

## <a name="steps"></a>Passi

Quando si crea una data factory, un'identità del servizio viene creata con la creazione della factory. L'identità del servizio è un'applicazione gestita registrata in Azure Activity Directory e rappresenta la data factory specifica. È possibile trovare le informazioni sull'identità del servizio dal portale di Azure -> Data factory -> Proprietà: 

- ID IDENTITÀ DEL SERVIZIO
- TENANT IDENTITÀ DEL SERVIZIO
- ID APPLICAZIONE IDENTITÀ DEL SERVIZIO

Per fare riferimento a una credenziale archiviata in Azure Key Vault, è necessario:

1. Copiare l'"ID APPLICAZIONE IDENTITÀ DEL SERVIZIO" generato con la data factory.
2. Concedere l'accesso dell'identità del servizio ad Azure Key Vault. Nell'insieme di credenziali delle chiavi -> Controllo di accesso -> Aggiungi -> cercare l'ID applicazione identità del servizio per aggiungere l'autorizzazione di lettura. Consente a questa factory designata di accedere al segreto nell'insieme di credenziali.
3. Creare un servizio collegato che punta ad Azure Key Vault. Fare riferimento a [Servizio collegato di Azure Key Vault](#azure-key-vault-linked-service).
4. Creare il servizio collegato di archiviazione dati, nel cui riferimento il segreto corrispondente è archiviato nell'insieme di credenziali delle chiavi. Fare riferimento a [Credenziale di riferimento archiviata nell'insieme di credenziali delle chiavi](#reference-credential-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Servizio collegato di Azure Key Vault

Per il servizio collegato di Azure Key Vault sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureKeyVault**. | Sì |
| baseUrl | Specificare l'URL di Azure Key Vault (nome DNS). | Sì |

**Esempio:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
        "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-credential-stored-in-key-vault"></a>Credenziali di riferimento archiviate nell'insieme di credenziali delle chiavi

Quando si configura un campo nel servizio collegato che fa riferimento a un segreto dell'insieme di credenziali delle chiavi, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type del campo deve essere impostata su: **AzureKeyVaultSecret**. | Sì |
| secretName | Il nome del segreto in Azure Key Vault. | Sì |
| secretVersion | La versione del segreto in Azure Key Vault.<br/>Se non specificata, usare sempre la versione più recente del segreto.<br/>Se specificata, corrisponde alla versione specificata.| No |
| store | Fa riferimento a un servizio collegato di Azure Key Vault che si usa per archiviare la credenziale. | Sì |

**Esempio: (vedere la sezione "password")**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
