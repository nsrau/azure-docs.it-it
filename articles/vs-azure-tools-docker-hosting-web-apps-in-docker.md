<properties
   pageTitle="Distribuire un contenitore ASP.NET in un host Docker remoto | Microsoft Azure"
   description="Imparare a usare Visual Studio Tools per Docker per pubblicare un'app Web ASP.NET 5 in un contenitore Docker in esecuzione su un computer host Docker di Azure"   
   services="visual-studio-online"
   documentationCenter=".net"
   authors="tomarcher"
   manager="douge"
   editor=""/>

<tags
   ms.service="visual-studio-online"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/26/2016"
   ms.author="tarcher"/>

# Distribuire un contenitore ASP.NET in un host Docker remoto

## Panoramica
Docker è un motore contenitore leggero, simile in qualche modo a una macchina virtuale, che è possibile usare per ospitare applicazioni e servizi. Visual Studio supporta Docker in Ubuntu, CoreOS e Windows. In questa esercitazione viene usata l'estensione [Visual Studio 2015 Tools per Docker](http://aka.ms/DockerToolsForVS) per pubblicare un'app ASP.NET 5 in un host Docker in Azure.

## 1\. Prerequisiti
Per completare questa esercitazione sarà necessario svolgere le operazioni seguenti:

- Creare una macchina virtuale host Docker in Azure, come descritto in [Come usare Docker Machine in Azure](./virtual-machines/virtual-machines-linux-classic-docker-machine.md)
- Installare [Visual Studio 2015](https://www.visualstudio.com/it-IT/downloads/download-visual-studio-vs.aspx)
- Installare [Visual Studio 2015 Tools per Docker - Preview](http://aka.ms/DockerToolsForVS)

## 2\. Creare un'app Web ASP.NET 5
La procedura seguente illustrerà la creazione di un'app ASP.NET 5 di base che verrà usata in questa esercitazione.

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 3\. Aggiungere il supporto di Docker

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## 4\. Scegliere l'host Docker remoto

1.  In **Esplora soluzioni** di Visual Studio individuare la cartella **Proprietà** ed espanderla.
1.  Aprire il file *Docker.props*.

    ![Aprire il file Docker.props][0]

1.  Modificare il valore di **DockerMachineName** nel nome dell'host Docker remoto. Se non si conosce il nome dell'host Docker remoto, eseguire ```docker-machine ls``` al prompt di Windows PowerShell. Usare il valore elencato nella colonna **Nome** per l'host desiderato.

    ![Modificare il nome del computer Docker][1]

1.  Riavviare Visual Studio.

## 5\. Configurare l'endpoint dell'host Docker in Azure
Prima di distribuire l'app da Visual Studio in Azure, aggiungere l'endpoint 80 alla macchina virtuale host Docker in modo da visualizzare l'app dal browser in un secondo momento. Questa operazione può essere eseguita tramite il portale di Azure classico o tramite Windows PowerShell:

- **Usare il portale di Azure classico per configurare l'endpoint dell'host Docker in Azure**

    1.  Passare al [portale di Azure classico](https://manage.windowsazure.com/). 
    
    1.  Toccare **MACCHINE VIRTUALI**.
    
    1.  Selezionare la macchina virtuale dell'host Docker.
    
    1.  Toccare la scheda **ENDPOINT**.
    
    1.  Toccare **Aggiungi** (nella parte inferiore della pagina).
    
    1.  Seguire le istruzioni per esporre la porta 80, che viene usata dallo script di distribuzione per impostazione predefinita.

- **Usare Windows PowerShell per configurare l'endpoint dell'host Docker in Azure**

    1. Aprire Windows PowerShell ISE.
    1. Immettere il comando seguente al prompt di Windows PowerShell (modificando i valori in parentesi quadre in base al proprio ambiente):  

        ```PowerShell
        C:\PS>Get-AzureVM -ServiceName "<your_cloud_service_name>" -Name "<your_vm_name>" | Add-AzureEndpoint -Name "<endpoint_name>" -Protocol "tcp" -PublicPort 80 -LocalPort 80 | Update-AzureVM
        ```

## 6\. Compilare ed eseguire l'app
Durante la distribuzione su host remoti, la funzione di mapping volume usata per la modifica e l'aggiornamento dello sviluppo non funzionerà. È pertanto necessario usare la *configurazione per il rilascio* quando si compila un'applicazione per evitare la configurazione del mapping del volume. Svolvere le operazioni seguenti per eseguire l'app.

1.  Dalla barra degli strumenti di Visual Studio selezionare la configurazione per il **rilascio**

1.  Modificare la destinazione avviata in **Docker**.

1.  Toccare l'icona **Docker** icona per compilare ed eseguire l'app.

![Avviare l'app][2]

I risultati visualizzati dovrebbero essere simili ai seguenti.

![Visualizzare l'app][3]

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png

<!---HONumber=AcomDC_0330_2016-->