---
title: Distribuire un&quot;applicazione .NET in un contenitore in Azure Service Fabric | Microsoft Docs
description: Viene illustrato come creare un pacchetto di un&quot;applicazione .NET in Visual Studio in un contenitore Docker. La nuova app &quot;contenitore&quot; viene quindi distribuita in un cluster di Service Fabric.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/10/2017
ms.author: mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: a965fcb9dd5caf9656af5ae381b25baaaa01ec6d
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="deploy-a-net-app-in-a-container-to-azure-service-fabric"></a>Distribuire un'applicazione .NET in un contenitore in Azure Service Fabric

Questa esercitazione illustra come distribuire un'applicazione ASP.NET esistente in un contenitore Windows usando Visual Studio 2017 Update 3 Preview. Viene quindi illustrato come distribuire il contenitore in Azure usando Visual Studio Team Services e come ospitarlo in un cluster di Service Fabric.

## <a name="prerequisites"></a>Prerequisiti

1. Installare [Docker CE per Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) in modo da poter eseguire i contenitori in Windows 10.
2. Acquisire familiarità con le informazioni riportate nella [Guida introduttiva ai contenitori di Windows 10][link-container-quickstart].
3. Per questo articolo viene usata **Fabrikam Fiber**, un'app di esempio che è possibile scaricare [qui][link-fabrikam-github].
4. [Azure PowerShell][link-azure-powershell-install]
5. [Estensione Strumenti di recapito continuo per Visual Studio 2017][link-visualstudio-cd-extension]

>[!NOTE]
>Se questa è la prima volta che si eseguono immagini di contenitori Windows sul computer in uso, è necessario che Docker CE ottenga le immagini di base per i contenitori. Le dimensioni delle immagini usate in questa esercitazione sono pari a 14 GB. Procedere ed eseguire il comando seguente in PowerShell per ottenere le immagini di base:
>
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

## <a name="containerize-the-application"></a>Distribuire l'applicazione in un contenitore

Per avviare l'esecuzione dell'applicazione in un contenitore, è necessario aggiungere il **supporto per Docker** al progetto in Visual Studio. Quando si aggiunge il **supporto per Docker** all'applicazione, si verifica quanto segue. In primo luogo, al progetto viene aggiunto un file _docker_. Questo nuovo file descrive il modo in cui l'immagine del contenitore deve essere compilata. In secondo luogo, alla soluzione viene aggiunto un nuovo progetto _docker-compose_. Questo nuovo progetto contiene alcuni file docker-compose che possono essere usati per descrivere la modalità di esecuzione del contenitore.

Per altre informazioni, vedere [Visual Studio Container Tools][link-visualstudio-container-tools].

### <a name="add-docker-support"></a>Aggiungere il supporto di Docker

1. Aprire il file **FabrikamFiber.CallCenter.sln** in Visual Studio

2. Fare clic con il pulsante destro del mouse sul progetto **FabrikamFiber.Web** > **Aggiungi** > **Supporto Docker**.

### <a name="add-support-for-sql"></a>Aggiungere il supporto per SQL

Questa applicazione usa SQL come provider di dati, motivo per cui per eseguire l'applicazione è necessaria un'istanza di SQL Server. In questa esercitazione viene usato SQL Server in esecuzione in un contenitore.
Per indicare a Docker che si vuole eseguire un'istanza di SQL Server in un contenitore, è possibile fare riferimento a un'immagine del contenitore SQL Server nel file docker-compose.override.yml nel progetto docker-compose. In questo modo, l'istanza di SQL Server in esecuzione nel contenitore viene usata quando si esegue il debug dell'applicazione in Visual Studio.

1. Aprire **Esplora soluzioni**.

2. Aprire **docker-compose** > **docker-compose.yml** > **docker-compose.override.yml**.

3. Sotto il nodo `services:` aggiungere un nuovo nodo denominato `db:`. Questo nodo dichiara l'esecuzione di un'istanza di SQL Server in un contenitore.

   ```yml
     db:
       image: microsoft/mssql-server-windows-developer
       environment:
         sa_password: "Password1"
         ACCEPT_EULA: "Y"
       ports:
         - "1433"
       healthcheck:
         test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
         interval: 1s
         retries: 20
   ```

   >[!NOTE]
   >Per il debug locale è possibile usare un'istanza di SQL Server a scelta, a condizione che sia raggiungibile dall'host. **localdb**, tuttavia, non supporta la comunicazione `container -> host`.

   >[!NOTE]
   >Se si vuole eseguire sempre l'istanza di SQL Server in un contenitore, è possibile scegliere di aggiungere quanto sopra riportato al file docker-compose.yml anziché al file docker-compose.override.yml file.


4. Modificare il nodo `fabrikamfiber.web` e aggiungere il nuovo nodo figlio denominato `depends_on:`. Il questo modo si assicura che il servizio `db` (il contenitore SQL Server) venga avviato prima dell'applicazione Web (fabrikamfiber.web).

   ```yml
     fabrikamfiber.web:
       ports:
         - "80"
       depends_on:
         - db
   ```

5. Nel progetto **FabrikamFiber.Web** aggiornare la stringa di connessione nel file **web.config** in modo che punti all'istanza di SQL Server nel contenitore.

   ```xml
   <add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
   
   <add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
   ```

   >[!NOTE]
   >Se si desidera usare un'istanza di SQL Server diversa durante la creazione di una compilazione di rilascio dell'applicazione Web, aggiungere un'altra stringa di connessione al file web.release.config.

6. Premere **F5** per eseguire l'applicazione ed eseguirne il debug nel contenitore.

   Edge apre la pagina di avvio definita dell'applicazione usando l'indirizzo IP del contenitore nella rete NAT interna (in genere 172.x.x.x). Per altre informazioni sul debug delle applicazioni nei contenitori mediante Visual Studio 2017, vedere [questo articolo][link-debug-container].

   ![esempio di fabrikam in un contenitore][image-web-preview]

L'applicazione è ora pronta per la compilazione e l'inserimento come pacchetto in un contenitore. Dopo aver creato l'immagine del contenitore sul computer, è possibile eseguirne il push in qualsiasi registro contenitori e quindi estrarla in qualsiasi host per l'esecuzione.

Nella rimanente parte dell'esercitazione verrà usato Visual Studio Team Services per compilare e distribuire il contenitore, eseguirne il push nel Registro contenitori di Azure e distribuirlo in Service Fabric, in esecuzione in Azure.

## <a name="create-a-service-fabric-cluster"></a>Creare un cluster di Service Fabric

Se si dispone già di un cluster di Service Fabric in cui distribuire l'applicazione, è possibile ignorare questo passaggio. In caso contrario, procedere e creare un cluster di Service Fabric.

>[!NOTE]
>La procedura seguente consente di creare un cluster di Service Fabric, protetto da un certificato autofirmato che è posizionato in un insieme di credenziali delle chiavi creato come parte della distribuzione. Per altre informazioni sull'uso dell'autenticazione di Azure Active Directory, vedere l'articolo [Creare un cluster di Service Fabric usando Azure Resource Manager][link-servicefabric-create-secure-clusters].

1. Scaricare una copia locale dei file modello e di parametri a cui viene fatto riferimento nelle risorse seguenti.
    * [Azure Resource Manager template for Service Fabric][link-sf-clustertemplate] (Modello di Azure Resource Manager per Service Fabric): modello di Resource Manager che definisce un cluster di Service Fabric.
    * [File di parametri _modello][link-sf-clustertemplate-parameters]: file di parametri per la personalizzazione della distribuzione cluster.
2. Personalizzare i parametri seguenti nel file di parametri:
  
   | Parametro       | Descrizione |
   | --------------- | ----------- |
   | clusterName     | Nome del cluster. |
   | adminUserName   | Account dell'amministratore locale nelle macchine virtuali del cluster. |
   | adminPassword   | Password dell'amministratore locale nelle macchine virtuali del cluster. |
   | clusterLocation | Area di Azure per la distribuzione del cluster. |
   | vmInstanceCount | Numero di nodi del cluster (può essere 1) |

3. Aprire **PowerShell**.
4. **Accedere** ad Azure.

   ```powershell
   Login-AzureRmAccount
   ```

5. Selezionare la **sottoscrizione** in cui si vuole distribuire il cluster.

   ```powershell
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

6. Creare e **crittografare una password** per il certificato usato da Service Fabric.

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```

7. **Creare il cluster** eseguendo il comando seguente:

   ```powershell
   New-AzureRmServiceFabricCluster 
       -TemplateFile C:\users\me\downloads\PreviewSecureClusters.json `
       -ParameterFile C:\users\me\downloads\myCluster.parameters.json `
       -CertificateOutputFolder C:\users\me\desktop\ `
       -CertificatePassword $pwd `
       -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
       -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >Il parametro `-CertificateSubjectName` deve essere allineato al parametro clusterName specificato nel file dei parametri del dominio associato all'area di Azure scelta, ad esempio: `clustername.eastus.cloudapp.azure.com`.
   
    Al termine della configurazione, verranno visualizzate le informazioni sul cluster creato in Azure, nonché la copia del certificato nella directory -CertificateOutputFolder.

8. **Fare doppio clic** sul certificato da installare nel computer locale.

## <a name="deploy-with-visual-studio"></a>Distribuire con Visual Studio

Per configurare la distribuzione con Visual Studio Team Services, è necessario installare l'[estensione Strumenti di recapito continuo per Visual Studio 2017][link-visualstudio-cd-extension]. Questa estensione facilita la distribuzione in Azure mediante la configurazione di Visual Studio Team Services e consente di distribuire l'app nel cluster di Service Fabric.

Per iniziare, il codice deve essere ospitato nel controllo del codice sorgente. Nella restante parte della sezione si presuppone l'uso di **git**.

1. Nell'angolo inferiore destro di Visual Studio fare clic su **Aggiungi al controllo del codice sorgente** > **Git** (o su qualsiasi opzione desiderata).

   ![fare clic sul pulsante del controllo del codice sorgente][image-source-control]

2. Nel riquadro _Team Explorer_ premere **Pubblica repository GIT**.

3. Selezionare il nome dell'archivio VSTS e premere **Repository**.

   ![pubblicazione dell'archivio in VSTS][image-publish-repo]

Ora che il codice è sincronizzato con l'archivio del codice sorgente VSTS, è possibile configurare la distribuzione continua e il recapito continuo.

1. In _Esplora soluzioni_ fare clic con il pulsante destro del mouse sulla **soluzione** > **Configura recapito continuo**.

2. Selezionare la sottoscrizione di Azure.

3. Impostare **Tipo host** su **Cluster di Service Fabric**.

   >[!NOTE]
   >In base ai tipi di contenitori in fase di compilazione, vengono aggiunte altre opzioni per l'hosting dell'applicazione in contenitori di Azure. 

4. Impostare **Target Host** (Host di destinazione) sul cluster di Service Fabric creato nella sezione precedente.

5. Scegliere un **Registro contenitori** in cui pubblicare il contenitore.

   >[!TIP]
   >Usare il pulsante **Modifica** per creare un registro contenitori.
    
6. Premere **OK**.

   ![impostazione dell'integrazione continua di Service Fabric][image-setup-ci]
   
   Dopo il completamento della configurazione, il contenitore verrà distribuito in Service Fabric ogni volta che si esegue il push di aggiornamenti nell'archivio.

7. **Avviare una compilazione** usando **Team Explorer** e visualizzare l'applicazione contenitore in esecuzione in Service Fabric.

Dopo aver inserito la soluzione Fabrikam Call in un pacchetto e averla distribuita, è possibile aprire il [portale di Azure ][link-azure-portal] e visualizzare l'applicazione in esecuzione in Service Fabric. Per verificare l'applicazione, aprire un Web browser e passare all'URL del cluster di Service Fabric.

## <a name="next-steps"></a>Passaggi successivi

- [Strumenti dei contenitori in Visual Studio][link-visualstudio-container-tools]
- [Introduzione ai contenitori in Service Fabric][link-servicefabric-containers]
- [Creazione di applicazioni Service Fabric][link-servicefabric-createapp]

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-sf-clustertemplate-parameters]: https://aka.ms/securepreviewonelineclusterparameters

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png

