---
title: Ottenere informazioni sulla chiave di crittografia
titleSuffix: Azure Cognitive Search
description: Recuperare il nome e la versione della chiave di crittografia utilizzati in una mappa di indici o sinonimi in modo da poter gestire la chiave in Azure Key Vault.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.openlocfilehash: f6e356f868cdb2107a19084070a85a0388ab4af7
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87554867"
---
# <a name="get-customer-managed-key-information-from-indexes-and-synonym-maps"></a>Ottenere informazioni sulle chiavi gestite dal cliente da indici e mappe sinonimi

In ricerca cognitiva di Azure, le chiavi di crittografia gestite dal cliente vengono create, archiviate e gestite in Azure Key Vault. Se è necessario determinare se un oggetto è crittografato o se è stato usato il nome o la versione della chiave, usare l'API REST o un SDK per recuperare la proprietà **encryptionKey** da una definizione di mappa con indice o sinonimo. 

Si consiglia di [abilitare la registrazione](../key-vault/general/logging.md) in Key Vault in modo che sia possibile monitorare l'utilizzo delle chiavi.

## <a name="get-the-admin-api-key"></a>Ottenere la chiave API di amministrazione

Per ottenere le definizioni degli oggetti da un servizio di ricerca, è necessario eseguire l'autenticazione con diritti di amministratore. Il modo più semplice per ottenere la chiave API di amministrazione è tramite il portale.

1. Accedere al [portale di Azure](https://portal.azure.com/) e aprire la pagina di panoramica del servizio di ricerca.

1. Sul lato sinistro fare clic su **chiavi** e copiare un'API di amministrazione. È necessaria una chiave di amministratore per il recupero della mappa di indici e sinonimi.

Per i passaggi rimanenti, passare a PowerShell e all'API REST. Il portale non Mostra le mappe dei sinonimi, né le proprietà della chiave di crittografia degli indici.

## <a name="use-powershell-and-rest"></a>Usare PowerShell e REST

Eseguire i comandi seguenti per impostare le variabili e ottenere le definizioni degli oggetti.

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la chiave e la versione di crittografia utilizzate, è possibile gestire la chiave in Azure Key Vault o controllare altre impostazioni di configurazione.

+ [Avvio rapido: Impostare e recuperare un segreto da Azure Key Vault usando PowerShell](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-powershell)

+ [Configurare chiavi gestite dal cliente per la crittografia dei dati in Azure ricerca cognitiva](search-security-manage-encryption-keys.md)