---
title: Sottoscrizioni di Azure per enti pubblici | Documentazione Microsoft
description: Informazioni sulla gestione della sottoscrizione in Azure per enti pubblici
services: Azure-Government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
editor: 
ms.assetid: d3375e84-a37d-4e44-9040-70dbe08eabfc
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/21/2016
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 40432290f50ce08320fef1604e764b5a93a9eb0b
ms.openlocfilehash: a49552a68a0f1f077d4416cb2ba70fe2f7a632ff


---
# <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>Gestione della sottoscrizione e connessione a essa in Azure per enti pubblici
Azure per enti pubblici offre endpoint e URL univoci per la gestione dell'ambiente. È importante usare le connessioni appropriate per gestire l'ambiente tramite il portale o PowerShell. Una volta stabilita la connessione all'ambiente di Azure per enti pubblici, le normali operazioni per la gestione di un servizio funzionano se il componente è stato distribuito.

## <a name="connecting-via-the-portal"></a>Connessione tramite il portale
Il portale rappresenta il modo principale usato dalla maggior parte delle persone per connettersi ad Azure per enti pubblici.  Per connettersi, accedere al portale all'indirizzo [https://portal.azure.us](https://portal.azure.us).  È possibile accedere alla versione precedente del portale di Azure all'indirizzo [https://manage.windowsazure.us](https://manage.windowsazure.us).

È possibile creare sottoscrizioni per l'account connettendosi a [https://account.windowsazure.us](https://account.windowsazure.us).

## <a name="connecting-via-powershell"></a>Connessione tramite PowerShell
Se si usa Azure PowerShell per gestire una sottoscrizione di grandi dimensioni tramite funzionalità di accesso o script che non sono attualmente disponibili nel portale di Azure, è necessario connettersi ad Azure per enti pubblici invece che alla versione pubblica di Azure.  Se è stato usato PowerShell nella versione pubblica di Azure, è essenzialmente la stessa cosa.  Le differenze in Azure per enti pubblici sono le seguenti:

* Connessione dell'account
* Nomi delle aree

> [!NOTE]
> Se non è stato ancora usato PowerShell, vedere l'[introduzione ad Azure PowerShell](../powershell-install-configure.md).
> 
> 

Quando si avvia PowerShell, è necessario indicare ad Azure PowerShell di connettersi ad Azure per enti pubblici specificando un parametro di ambiente.  Il parametro assicura che PowerShell si connetta agli endpoint corretti.  La raccolta di endpoint viene determinata durante l'accesso al proprio account.  API diverse richiedono versioni diverse dell'opzione Environment:

| Tipo di connessione | Comando |
| --- | --- |
| Comandi di [gestione dei servizi](https://msdn.microsoft.com/library/dn708504.aspx) |`Add-AzureAccount -Environment AzureUSGovernment` |
| Comandi di [gestione delle risorse](https://msdn.microsoft.com/library/mt125356.aspx) |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| Comandi di [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) |`Connect-MsolService -AzureEnvironment UsGovernment` |
| [Comandi di Azure Active Directory v2](https://msdn.microsoft.com/library/azure/mt757189.aspx) |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Riga di comando dell'interfaccia della riga di comando di Azure](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

È anche possibile usare l'opzione Environment quando ci si connette a un account di archiviazione usando New-AzureStorageContext e specificando AzureUSGovernment.

### <a name="determining-region"></a>Determinazione dell'area
Una volta stabilita la connessione, un'altra differenza è rappresentata dalle aree usate come destinazione per un servizio.  Ogni cloud Azure ha aree diverse.  Le aree sono elencate nella pagina relativa alla disponibilità del servizio.  In genere si usano le aree nel parametro Location per un comando.

C'è un aspetto da considerare.  Le aree di Azure per enti pubblici devono essere formattate in modo diverso rispetto ai nomi comuni:

| Nome comune | Comando |
| --- | --- |
| US Gov Virginia |USGov Virginia |
| US Gov Iowa |USGov Iowa |

> [!NOTE]
> Non c'è alcuno spazio tra US e Gov quando si usa il parametro Location.
> 
> 

Per convalidare le aree disponibili in Azure per enti pubblici, è possibile eseguire i comandi seguenti e stampare l'elenco corrente:

    Get-AzureLocation

Per informazioni sugli ambienti disponibili in Azure, è possibile eseguire:

    Get-AzureEnvironment

## <a name="connecting-via-visual-studio"></a>Connessione tramite Visual Studio
Visual Studio viene usato dagli sviluppatori per gestire facilmente le sottoscrizioni di Azure durante la compilazione di soluzioni.  Visual Studio attualmente non consente di configurare una connessione ad Azure per enti pubblici nell'interfaccia utente.  

### <a name="updating-visual-studio-for-azure-government"></a>Aggiornamento di Visual Studio per Azure per enti pubblici
Per consentire la connessione di Visual Studio ad Azure per enti pubblici, è necessario aggiornare il Registro di sistema.

1. Chiudere Visual Studio.
2. Creare un file di testo denominato **VisualStudioForAzureGov.reg**.
3. Copiare il testo seguente e incollarlo in **VisualStudioForAzureGov.reg**:
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login-us.microsoftonline.com/"
        "adaluri"="https://management.core.usgovcloudapi.net"
        "AzureRMEndpoint"="https://management.usgovcloudapi.net"
        "AzureRMAudienceEndpoint"="https://management.core.usgovcloudapi.net"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.windows.net"
4. Salvare il file e quindi eseguirlo facendo doppio clic su di esso.  Verrà richiesto di unire il file al Registro di sistema.
5. Avviare Visual Studio e iniziare a usare [Cloud Explorer](../vs-azure-tools-resources-managing-with-cloud-explorer.md)

> [!NOTE]
> Dopo l'impostazione della chiave del Registro di sistema, saranno accessibili solo le sottoscrizioni di Azure per enti pubblici.  Le sottoscrizioni configurate in precedenza sono ancora visibili, ma non funzionano perché Visual Studio è ora connesso ad Azure per enti pubblici anziché alla versione pubblica di Azure.  Per annullare le modifiche, vedere la procedura illustrata nella sezione seguente.
> 
> 

### <a name="reverting-visual-studio-connection-to-azure-government"></a>Annullamento della connessione di Visual Studio ad Azure per enti pubblici
Per consentire la connessione di Visual Studio alla versione pubblica di Azure, è necessario rimuovere le impostazioni del Registro di sistema che abilitano la connessione ad Azure per enti pubblici.

1. Chiudere Visual Studio.
2. Creare un file di testo denominato **VisualStudioForAzureGov_Remove.reg**.
3. Copiare il testo seguente e incollarlo in **VisualStudioForAzureGov_Remove.reg**:
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
4. Salvare il file e quindi eseguirlo facendo doppio clic su di esso.  Verrà richiesto di unire il file al Registro di sistema.
5. Avviare Visual Studio.

> [!NOTE]
> Dopo l'annullamento della chiave del Registro di sistema, le sottoscrizioni di Azure per enti pubblici vengono visualizzate ma non sono accessibili  e possono essere rimosse.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere:

* [Documentazione di PowerShell in GitHub](https://github.com/Azure/azure-powershell)
* [Istruzioni dettagliate per la connessione a Resource Manager](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/)
* [Documentazione di Azure PowerShell in MSDN](https://msdn.microsoft.com/library/mt619274.aspx)

Per maggiori informazioni e aggiornamenti, sottoscrivere il [blog di Microsoft Azure per enti pubblici](https://blogs.msdn.microsoft.com/azuregov/).




<!--HONumber=Nov16_HO3-->


