<properties 
   pageTitle="Come usare il prompt dei comandi di Azure PowerShell per creare un contenitore del servizio cloud vuoto" 
   description="Questo articolo illustra come creare un contenitore del servizio cloud ed eseguire le operazioni di gestione correlate al servizio cloud tramite lo script di PowerShell" 
   services="cloud-services" 
   documentationCenter=".net" 
   authors="cawaMS" 
   manager="" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="cawa"/>

# Come usare il prompt dei comandi di Azure PowerShell per creare un contenitore del servizio cloud vuoto
1. Installare i cmdlet di Microsoft Azure PowerShell dal [download di Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409). Per altre istruzioni sull'installazione del cmdlet di Azure PowerShell e sulla connessione alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](install-configurazione-powershell.md).

2. **New-AzureService** è il cmdlet per la creazione di un contenitore del servizio cloud vuoto.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>] 
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

   Un esempio di richiamo del cmdlet è il seguente: 
```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

3. Per altre informazioni sulla creazione di un servizio cloud di Azure, eseguire: 
```
Get-help New-AzureService
```

4. Passaggi successivi:

   - Per gestire la distribuzione del servizio cloud, fare riferimento ai comandi [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) e [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx). Inoltre, fare riferimento a [Come configurare i servizi cloud](cloud-services-come-a-configure.md)

    - Per pubblicare il progetto servizio cloud in Azure, consultare **publishcloudservice. Ps1** nell'esempio di codice riportato nell'articolo [Recapito continuo per Servizi cloud in Azure](cloud-services-dotnet-continua-delivery.md)


    


<!--HONumber=52-->