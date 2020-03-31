---
title: Creare e pubblicare a.Net'app Core in un cluster Linux remoto
description: Creare e pubblicare app .Net Core destinate a un cluster Linux remoto da Visual StudioCreate and publish .Net Core apps targeting a remote Linux cluster from Visual Studio
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614350"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Usare Visual Studio per creare e pubblicare applicazioni .Net Core destinate a un cluster Linux Service Fabric remotoUse Visual Studio to create and publish .Net Core applications targeting a remote Linux Service Fabric cluster
Con gli strumenti di Visual Studio è possibile sviluppare e pubblicare applicazioni .Net Core di Service Fabric destinate a un cluster Linux Service Fabric. La versione SDK deve essere 3.4 o superiore per distribuire un'applicazione .Net Core destinata a cluster Linux Service Fabric da Visual Studio.

> [!Note]
> Visual Studio doesn't support debugging Service Fabric applications which target Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Creare un'applicazione di Service Fabric destinata a .Net CoreCreate a Service Fabric application targeting .Net Core
1. Avviare Visual Studio come **amministratore**.
2. Creare un progetto con Progetto >**Nuovo >progetto**.
3. Nella finestra di dialogo **Nuovo progetto** scegliere **Cloud -> Service Fabric Application**.
![create-application]
4. Assegnare un nome all'applicazione e fare clic su **OK**.
5. Nella pagina Nuovo servizio **Service Fabric** selezionare il tipo di servizio che si desidera creare nella **sezione .Net Core**.
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>Distribuire in un cluster Linux remotoDeploy to a remote Linux cluster
1. In Esplora soluzioni fare clic con il pulsante destro del mouse sull'applicazione e **scegliere Compila**.
![build-application (applicazione di compilazione)]
2. Una volta completato il processo di compilazione per l'applicazione, fare clic con il pulsante destro del mouse sul servizio e selezionare Modifica **file csproj**.
![edit-csproj]
3. Modificare la proprietà UpdateServiceFabricManifestEnabled da True a **False** se il servizio è un tipo di **progetto attore.** Se l'applicazione non dispone di un servizio attore, andare al passaggio 4.If your application does not have an actor service, skip to step 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Impostando UpdateServiceFabricManifestEnabled su false, verranno disabilitati gli aggiornamenti di ServiceManifest.xml durante una compilazione. Qualsiasi modifica, ad esempio add, remove, o rename to the service non verrà riflessa nel file ServiceManifest.xml. Se vengono apportate modifiche, è necessario aggiornare manualmente ServiceManifest o impostare temporaneamente UpdateServiceFabricManifestEnabled su true e compilare il servizio che aggiornerà il file ServiceManifest.xml e quindi ripristinarlo su false.
>

4. Aggiornare RuntimeIndetifier da win7-x64 alla piattaforma di destinazione nel progetto di servizio.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. In ServiceManifest aggiornare il programma del punto di ingresso per rimuovere il file con estensione exe. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. In Esplora soluzioni fare clic con il pulsante destro del mouse sull'applicazione e **scegliere Pubblica**. Viene visualizzata la finestra di dialogo **Pubblica**.
7. In **Endpoint di connessione**selezionare l'endpoint per il cluster Linux di Service Fabric remoto di destinazione.
![pubblicare-applicazione]

<!--Image references-->
[create-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[build-application (applicazione di compilazione)]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[pubblicare-applicazione]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su [Introduzione a Service Fabric con .Net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
