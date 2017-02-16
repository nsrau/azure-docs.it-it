---
title: Publish-WebApplicationVM | Documentazione Microsoft
description: Informazioni su come distribuire un&quot;applicazione Web in una macchina virtuale. Se non sono presenti, lo script crea le risorse necessarie nella sottoscrizione di Azure.
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: de4cec95-f73f-44d9-babd-9f47f2633cdb
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: bedcbc3218022b36cc9f961c7621db3c5a639828


---
# <a name="publish-webapplicationvm-windows-powershell-script"></a>Publish-WebApplicationVM (Windows PowerShell script)
Consente di distribuire un'applicazione Web in una macchina virtuale. Se non sono presenti, lo script crea le risorse necessarie nella sottoscrizione di Azure.

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### <a name="configuration"></a>Configurazione
Percorso del file di configurazione JSON che descrive i dettagli della distribuzione.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione |denominata |
| Valore predefinito |nessuno |
| Input pipeline accettato? |false |
| Caratteri jolly accettati? |false |

### <a name="subscriptionname"></a>SubscriptionName
Nome della sottoscrizione di Azure in cui creare la macchina virtuale.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |false |
| Posizione |denominata |
| Valore predefinito |Usa la prima sottoscrizione nel file di sottoscrizione |
| Input pipeline accettato? |false |
| Caratteri jolly accettati? |false |

### <a name="webdeploypackage"></a>WebDeployPackage
Percorso al pacchetto di distribuzione Web da pubblicare nella macchina virtuale. È possibile creare questo pacchetto usando la pubblicazione Web guidata di Visual Studio. Vedere [Procedura: Creare un pacchetto di distribuzione Web in Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx).

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |false |
| Posizione |denominata |
| Valore predefinito |nessuno |
| Input pipeline accettato? |false |
| Caratteri jolly accettati? |false |

### <a name="allowuntrusted"></a>AllowUntrusted
Se true, consente l'utilizzo di certificati che non sono firmati da un'autorità radice attendibile.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |false |
| Posizione |denominata |
| Valore predefinito |false |
| Input pipeline accettato? |false |
| Caratteri jolly accettati? |false |

### <a name="vmpassword"></a>VMPassword
Le credenziali per l'account della macchina virtuale. Esempio: -VMPassword @{Name = "admin"; Password = "password"}

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |false |
| Posizione |denominata |
| Valore predefinito |nessuno |
| Input pipeline accettato? |false |
| Caratteri jolly accettati? |false |

### <a name="databaseserverpassword"></a>DatabaseServerPassword
Le credenziali del database SQL in Azure. Esempio: -DatabaseServerPassword @{Name = "admin"; Password = "password"}

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |false |
| Posizione |denominata |
| Valore predefinito |nessuno |
| Input pipeline accettato? |false |
| Caratteri jolly accettati? |false |

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
Se impostato su true, stampa i messaggi dallo script al flusso di output.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |false |
| Posizione |denominata |
| Valore predefinito |false |
| Input pipeline accettato? |false |
| Caratteri jolly accettati? |false |

## <a name="remarks"></a>Osservazioni
Per una spiegazione completa sull'uso dello script per creare ambienti di sviluppo e test, vedere [Uso degli script di Windows PowerShell per la pubblicazione in ambienti di sviluppo e test](vs-azure-tools-publishing-using-powershell-scripts.md).

Il file di configurazione JSON specifica i dettagli degli elementi da distribuire. Include le informazioni specificate al momento della creazione del progetto, ad esempio il nome, il set di affinità, l’immagine VHD e la dimensione della macchina virtuale. Inoltre include gli endpoint nella macchina virtuale, i database per eseguire il provisioning, se presente, e i parametri di distribuzione Web. Il codice seguente mostra un esempio di file di configurazione JSON:

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

È possibile modificare il file di configurazione JSON per cambiare gli elementi del provisioning. Una macchina virtuale e un servizio cloud sono necessari, ma la sezione del database è facoltativa.




<!--HONumber=Nov16_HO3-->


