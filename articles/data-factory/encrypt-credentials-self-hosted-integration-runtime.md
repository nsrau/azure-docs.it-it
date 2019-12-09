---
title: Crittografare le credenziali in Azure Data Factory
description: Altre informazioni su come crittografare e archiviare le credenziali per gli archivi dati locali in un computer con il runtime di integrazione self-hosted.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: bcca2e6168baafe9f98f663298790841c0f1f450
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927421"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Crittografare le informazioni sulle credenziali per gli archivi dati locali in Azure Data Factory
È possibile crittografare e archiviare le credenziali per gli archivi dati locali (servizi collegati con informazioni riservate) in un computer con il runtime di integrazione self-hosted. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Si passa un file di definizione JSON con le credenziali per il <br/>Cmdlet [**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) per produrre un file di definizione JSON di output con le credenziali crittografate. Quindi, è possibile utilizzare la definizione JSON aggiornata per creare i servizi collegati.

## <a name="author-sql-server-linked-service"></a>Servizio collegato di SQL Server autore
Creare un file JSON denominato **SqlServerLinkedService.json** in qualsiasi cartella con il seguente contenuto:  

Sostituire `<servername>`, `<databasename>`, `<username>` e `<password>` con i valori per SQL Server prima di salvare il file. Inoltre, sostituire `<integration runtime name>` con il nome del runtime di integrazione. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
            }
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Crittografare le credenziali
Per crittografare i dati sensibili dal payload JSON in un runtime di integrazione self-hosted locale, eseguire **New-AzDataFactoryV2LinkedServiceEncryptedCredential**e passare il payload JSON. Questo cmdlet assicura che le credenziali vengano crittografate utilizzando DPAPI e archiviate nel nodo del runtime di integrazione self-hosted in locale. Il payload di output contenente il riferimento crittografato alla credenziale può essere reindirizzato a un altro file JSON (in questo caso ' encryptedLinkedService. JSON ').

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Usare JSON con credenziali crittografate
A questo punto, utilizzare il file JSON di output del comando precedente contenente le credenziali crittografate per impostare il **SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle considerazioni relative alla sicurezza per lo spostamento dei dati, vedere [Considerazioni relative alla sicurezza per lo spostamento dei dati](data-movement-security-considerations.md).

