<properties
   pageTitle="Creare un contenitore del servizio cloud con PowerShell | Microsoft Azure"
   description="Questo articolo illustra come creare un contenitore del servizio cloud con PowerShell. Il contenitore ospita il ruolo Web e il ruolo di lavoro."
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="04/25/2016"
   ms.author="cawa"/>

# Usare un prompt dei comandi di Azure PowerShell per creare un contenitore del servizio cloud vuoto
Questo articolo illustra come creare velocemente un contenitore di Servizi cloud usando i cmdlet di Azure PowerShell. Eseguire i passaggi seguenti:

1. Installare i cmdlet di Microsoft Azure PowerShell dalla pagina dei [download di Azure PowerShell](http://aka.ms/webpi-azps).
2. Aprire il prompt dei comandi di PowerShell.
3. Usare [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) per accedere.

    > [AZURE.NOTE] Per altre istruzioni sull'installazione del cmdlet di Azure PowerShell e sulla connessione alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

4. Usare il cmdlet **New-AzureService** per creare un contenitore del servizio cloud vuoto.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

5. Per richiamare il cmdlet, seguire questo esempio:
```
New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
```

Per altre informazioni sulla creazione del servizio cloud di Azure, eseguire:
```
Get-help New-AzureService
```

### Passaggi successivi

 * Per gestire la distribuzione del servizio cloud, vedere i comandi [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) e [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx). Per altre informazioni, è anche possibile vedere [Come configurare i servizi cloud](cloud-services-how-to-configure.md).

 * Per pubblicare il progetto servizio cloud in Azure, vedere l'esempio di codice **PublishCloudService.ps1** riportato nell'articolo [Recapito continuo per il servizio cloud in Azure](cloud-services-dotnet-continuous-delivery.md).

<!---HONumber=AcomDC_0504_2016-->