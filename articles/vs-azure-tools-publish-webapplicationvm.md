<properties
   pageTitle="Publish-WebApplicationVM | Microsoft Azure"
   description="Informazioni su come distribuire un'applicazione Web in una macchina virtuale. Se non sono presenti, lo script crea le risorse necessarie nella sottoscrizione di Azure."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="04/18/2016"
   ms.author="tarcher" />

# Publish-WebApplicationVM (Windows PowerShell script)

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

### Configurazione

Percorso del file di configurazione JSON che descrive i dettagli della distribuzione.

|Alias|nessuno|
|---|---|
|Obbligatorio?|true|
|Posizione|denominata|
|Valore predefinito|nessuno|
|Input pipeline accettato?|false|
|Caratteri jolly accettati?|false|

### SubscriptionName

Il nome della sottoscrizione di Azure in cui si desidera creare la macchina virtuale.

|Alias|nessuno|
|---|---|
|Obbligatorio?|false|
|Posizione|denominata|
|Valore predefinito|Utilizza la prima sottoscrizione nel file di sottoscrizione|
|Input pipeline accettato?|false|
|Caratteri jolly accettati?|false|

### WebDeployPackage

Percorso al pacchetto di distribuzione Web da pubblicare nella macchina virtuale. È possibile creare questo pacchetto usando la pubblicazione Web guidata di Visual Studio. Vedere [Procedura: Creare un pacchetto di distribuzione Web in Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx).

|Alias|nessuno|
|---|---|
|Obbligatorio?|false|
|Posizione|denominata|
|Valore predefinito|nessuno|
|Input pipeline accettato?|false|
|Caratteri jolly accettati?|false|

### AllowUntrusted

Se true, consente l'utilizzo di certificati che non sono firmati da un'autorità radice attendibile.

|Alias|nessuno|
|---|---|
|Obbligatorio?|false|
|Posizione|denominata|
|Valore predefinito|false|
|Input pipeline accettato?|false|
|Caratteri jolly accettati?|false|

### VMPassword

Le credenziali per l'account della macchina virtuale. Esempio: -VMPassword @{Name = "admin"; Password = "password"}

|Alias|nessuno|
|---|---|
|Obbligatorio?|false|
|Posizione|denominata|
|Valore predefinito|nessuno|
|Input pipeline accettato?|false|
|Caratteri jolly accettati?|false|

### DatabaseServerPassword

Le credenziali del database SQL in Azure. Esempio: -DatabaseServerPassword @{Name = "admin"; Password = "password"}

|Alias|nessuno|
|---|---|
|Obbligatorio?|false|
|Posizione|denominata|
|Valore predefinito|nessuno|
|Input pipeline accettato?|false|
|Caratteri jolly accettati?|false|

### SendHostMessagesToOutput

Se impostato su true, stampa i messaggi dallo script al flusso di output.

|Alias|nessuno|
|---|---|
|Obbligatorio?|false|
|Posizione|denominata|
|Valore predefinito|false|
|Input pipeline accettato?|false|
|Caratteri jolly accettati?|false|

## Osservazioni

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

<!---HONumber=AcomDC_0420_2016-->