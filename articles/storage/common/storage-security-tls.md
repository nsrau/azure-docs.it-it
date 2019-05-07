---
title: Abilitare la connessione TLS sicura per il client di Archiviazione di Azure | Microsoft Docs
description: Informazioni su come abilitare TLS 1.2 nel client di Archiviazione di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 218708ffc9a680150d7b6bf559a00ca87054bbe8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65152961"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Abilitare la connessione TLS sicura per il client di Archiviazione di Azure

Transport Layer Security (TLS) e Secure Sockets Layer (SSL) sono protocolli di crittografia che consentono di proteggere le comunicazioni su una rete di computer. SSL 1.0, 2.0 e 3.0 sono stati identificati come vulnerabili e non sono più consentiti da RFC. TLS 1.0 è diventato un protocollo non sicuro per l'uso della crittografia a blocchi (DES CBC e RC2 CBC) e di flusso (RC4). Il PCI Council ha anche suggerito la migrazione a versioni più recenti del protocollo TLS. Per informazioni più dettagliate, è possibile vedere [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

Archiviazione di Azure ha dismesso SSL 3.0 dal 2015 e usa TLS 1.2 negli endpoint HTTPS pubblici, ma i protocolli TLS 1.0 e TLS 1.1 sono ancora supportati per la compatibilità con le versioni precedenti.

Per essere certi che la connessione ad Archiviazione di Azure sia sicura e conforme, è necessario abilitare TLS 1.2 o versione successiva sul lato client prima di inviare richieste al servizio Archiviazione di Azure.

## <a name="enable-tls-12-in-net-client"></a>Abilitare TLS 1.2 nel client .NET

Per consentire al client di eseguire la negoziazione TLS 1.2, il sistema operativo e la versione di .NET Framework devono entrambi supportare TLS 1.2. Per informazioni più dettagliate, vedere [Supporto per TLS 1.2](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

L'esempio seguente mostra come abilitare TLS 1.2 nel client .NET.

```csharp

    static void EnableTls12()
    {
        // Enable TLS 1.2 before connecting to Azure Storage
        System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

        // Connect to Azure Storage
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName={yourstorageaccount};AccountKey={yourstorageaccountkey};EndpointSuffix=core.windows.net");
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        CloudBlobContainer container = blobClient.GetContainerReference("foo");
        container.CreateIfNotExists();
    }

```

## <a name="enable-tls-12-in-powershell-client"></a>Abilitare TLS 1.2 nel client PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)] 

L'esempio seguente mostra come abilitare TLS 1.2 nel client PowerShell.

```powershell
# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGroupName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers
```

## <a name="verify-tls-12-connection"></a>Verificare la connessione TLS 1.2

Per verificare se TLS 1.2 viene effettivamente usato, è possibile usare Fiddler. Aprire Fiddler per iniziare ad acquisire il traffico di rete client e quindi eseguire l'esempio precedente. È quindi possibile trovare la versione TLS nella connessione creata dall'esempio.

La schermata seguente presenta un esempio per la verifica.

![screenshot di verifica della versione TLS in Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Vedere anche 

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [Conformità PCI su TLS](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [Abilitare TLS nel client Java](https://www.java.com/en/configure_crypto.html)
