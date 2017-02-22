---
title: Automatizzare la distribuzione di un&quot;app di Azure con gli strumenti da riga di comando | Documentazione Microsoft
description: Informazioni su come distribuire un&quot;app di Azure dalla riga di comando
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 8b65980c-eb75-44a2-8e0f-f9eb9e617d16
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 283b1cfda82b4f96ad5148c522a4c9833cb4c381
ms.openlocfilehash: e0e2e65557911bcac06d4dc355f47e9331934f8a


---
# <a name="automate-deployment-of-your-azure-app-with-command-line-tools"></a>Automatizzare la distribuzione di un'app di Azure con gli strumenti da riga di comando
È possibile automatizzare la distribuzione di un'app di Azure con gli strumenti da riga di comando. In questo articolo sono elencati gli strumenti disponibili e i collegamenti utili che illustrano come usare questi strumenti nel flusso di lavoro di distribuzione. 

## <a name="a-namemsbuildaautomate-deployment-with-msbuild"></a><a name="msbuild"></a>Automatizzare la distribuzione con MSBuild
Se si usa l'[IDE di Visual Studio](#vs) per lo sviluppo è possibile servirsi di [MSBuild](http://msbuildbook.com/) per automatizzare qualsiasi operazione nell'IDE. È possibile configurare MSBuild in modo da usare [Distribuzione Web](#webdeploy) o [FTP/FTPS](#ftp) per copiare i file. Distribuzione Web consente inoltre di automatizzare molte altre attività relative alla distribuzione, ad esempio la distribuzione dei database.

Per ulteriori informazioni sulla distribuzione da riga di comando, vedere le risorse seguenti:

* [Distribuzione Web ASP.NET con Visual Studio: distribuzione da riga di comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Decima di una serie di esercitazioni sulla distribuzione in Azure con Visual Studio. Contiene informazioni su come utilizzare la riga di comando per eseguire la distribuzione dopo aver configurato i profili di pubblicazione in Visual Studio.
* [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build](http://msbuildbook.com/). Libro paperback che contiene alcuni capitoli su come utilizzare MSBuild per la distribuzione.

## <a name="a-namepowershellaautomate-deployment-with-windows-powershell"></a><a name="powershell"></a>Automatizzare la distribuzione con Windows PowerShell
In [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx)è possibile eseguire funzioni di distribuzione MSBuild o FTP. In tal caso, è inoltre possibile utilizzare una raccolta di cmdlet di Windows PowerShell che facilitano la chiamata dell'API di gestione RET di Azure.

Per altre informazioni, vedere le seguenti risorse:

* [Distribuire un'app Web collegata a un repository GitHub](app-service-web-arm-from-github-provision.md)
* [Eseguire il provisioning di un'app Web con un database SQL](app-service-web-arm-with-sql-database-provision.md)
* [Effettuare il provisioning di microservizi e distribuirli in modo prevedibile in Azure](app-service-deploy-complex-application-predictably.md)
* Capitolo dell'e-book relativo a come [automatizzare tutto e creare app per cloud reali con Azure](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything), in cui viene illustrato in che modo l'applicazione di esempio illustrata nell'e-book utilizza gli script di Windows PowerShell per creare un ambiente di testing Azure ed eseguirvi la distribuzione. Per ottenere collegamenti ad altri documenti su Azure PowerShell vedere la sezione relativa alle [risorse](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources).
* [Utilizzo degli script di Windows PowerShell per la pubblicazione in ambienti di sviluppo e test](../vs-azure-tools-publishing-using-powershell-scripts.md). Come usare gli script di distribuzione di Windows PowerShell generati da Visual Studio.

## <a name="a-nameapiaautomate-deployment-with-net-management-api"></a><a name="api"></a>Automatizzare la distribuzione con API di gestione .NET
È possibile scrivere il codice C# per eseguire funzioni MSBuild o FTP per la distribuzione. In tal caso, sarà possibile accedere all'API REST per la gestione per eseguire funzioni di gestione del sito.

Per ulteriori informazioni, vedere la risorsa seguente:

* Post del blog di Scott Hanselman sull'[automazione completa con le librerie di gestione di Azure e .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Contiene una presentazione dell'API di gestione .NET e collegamenti a ulteriore documentazione.

## <a name="a-namecliadeploy-from-azure-command-line-interface-azure-cli"></a><a name="cli"></a>Distribuire dall'interfaccia della riga di comando di Azure
È possibile usare la riga di comando nei computer Windows, Mac o Linux per eseguire distribuzioni tramite FTP. In tal caso, è anche possibile accedere all'API REST per la gestione di Azure usando l'interfaccia della riga di comando di Azure.

Per ulteriori informazioni, vedere la risorsa seguente:

* [Strumenti da riga di comando di Azure](https://azure.microsoft.com/downloads/). Pagina del portale su Azure.com per le informazioni sullo strumento da riga di comando.

## <a name="a-namewebdeployadeploy-from-web-deploy-command-line"></a><a name="webdeploy"></a>Distribuire dalla riga di comando di distribuzione Web
[Distribuzione Web](http://www.iis.net/downloads/microsoft/web-deploy) è il software Microsoft per la distribuzione in IIS che non solo offre funzioni intelligenti di sincronizzazione dei file, ma può anche eseguire o coordinare molte altre attività relative alla distribuzione che non è possibile automatizzare quando si utilizza il trasferimento FTP. Ad esempio, con Distribuzione Web è possibile distribuire un nuovo database oppure gli aggiornamenti al database assieme all'app Web. Distribuzione Web consente inoltre di ridurre al minimo i tempi richiesti per l'aggiornamento di un sito esistente dal momento che è in grado di copiare in maniera intelligente solo i file modificati. Il supporto per Distribuzione Web è incorporato in Microsoft Visual Studio e Team Foundation Server, ma è possibile automatizzare la distribuzione anche usando Distribuzione Web direttamente dalla riga di comando. I comandi di Distribuzione Web sono molti potenti, ma la curva di apprendimento può rivelarsi ripida.

## <a name="more-resources"></a>Altre risorse
Un'altra opzione per automatizzare la distribuzione dalla riga di comando consiste nell'usare un servizio basato su cloud, ad esempio [Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy). Per altre informazioni, vedere l'articolo relativo alla [distribuzione di applicazioni ASP.NET in Siti Web di Azure](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).

Per altre informazioni, vedere la risorsa seguente:

* [Simple Web Apps: Deployment](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog di David Ebbo riguardante uno strumento che ha creato per facilitare l'uso di Distribuzione Web.
* [Strumento di distribuzione Web](http://technet.microsoft.com/library/dd568996). sul sito Microsoft TechNet. Articolo datato, ma è ancora un buon punto di partenza.
* [Uso di Distribuzione Web](http://www.iis.net/learn/publish/using-web-deploy). sul sito Microsoft IIS.NET. Anche questo articolo è datato, ma è un buon punto di partenza.
* [Distribuzione Web ASP.NET con Visual Studio: distribuzione da riga di comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild è il motore di compilazione usato da Visual Studio e può inoltre essere usato dalla riga di comando per la distribuzione di applicazioni Web in App Web. Questa esercitazione fa parte di una serie dedicata principalmente alla distribuzione con Visual Studio.




<!--HONumber=Jan17_HO1-->


