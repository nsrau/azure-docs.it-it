---
title: Creare e pubblicare un'app a.Net core in un cluster Linux remoto
description: Creare e pubblicare app .NET Core destinate a un cluster Linux remoto da Visual Studio
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
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Usare Visual Studio per creare e pubblicare applicazioni .NET Core destinate a un cluster Linux Service Fabric remoto
Con gli strumenti di Visual Studio è possibile sviluppare e pubblicare Service Fabric .NET Core applicazioni destinate a un cluster di Service Fabric Linux. La versione dell'SDK deve essere 3,4 o successiva per distribuire un'applicazione .NET Core destinata a cluster Linux Service Fabric da Visual Studio.

> [!Note]
> Visual Studio non supporta il debug di applicazioni Service Fabric destinate a Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Creare un'applicazione Service Fabric di destinazione .NET Core
1. Avviare Visual Studio come **amministratore**.
2. Creare un progetto con il **progetto New->>di file**.
3. Nella finestra di dialogo **nuovo progetto** scegliere **Cloud-> Service Fabric applicazione**.
![Crea applicazione]
4. Assegnare un nome all'applicazione e fare clic su **OK**.
5. Nella pagina **nuovo servizio Service Fabric** selezionare il tipo di servizio che si desidera creare nella **sezione .NET Core**.
![Crea-servizio]

## <a name="deploy-to-a-remote-linux-cluster"></a>Eseguire la distribuzione in un cluster Linux remoto
1. In Esplora soluzioni fare clic con il pulsante destro del mouse sull'applicazione e selezionare **Compila**.
![compilazione-applicazione]
2. Al termine del processo di compilazione per l'applicazione, fare clic con il pulsante destro del mouse sul servizio e scegliere modifica **file csproj**.
![Edit-csproj]
3. Modificare la proprietà UpdateServiceFabricManifestEnabled da true a **false** se il servizio è un **tipo di progetto Actor**. Se l'applicazione non dispone di un servizio Actor, andare al passaggio 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Se si imposta UpdateServiceFabricManifestEnabled su false, gli aggiornamenti di ServiceManifest. XML vengono disabilitati durante una compilazione. Eventuali modifiche, ad esempio aggiungere, rimuovere o rinominare il servizio, non verranno riflesse in ServiceManifest. XML. Se vengono apportate modifiche, è necessario aggiornare manualmente il ServiceManifest o impostare temporaneamente UpdateServiceFabricManifestEnabled su true e compilare il servizio che aggiornerà ServiceManifest. XML e quindi ripristinarlo di nuovo su false.
>

4. Aggiornare RuntimeIndetifier da Win7-x64 alla piattaforma di destinazione nel progetto di servizio.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. In ServiceManifest aggiornare il programma EntryPoint per Remove. exe. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. In Esplora soluzioni fare clic con il pulsante destro del mouse sull'applicazione e scegliere **pubblica**. Viene visualizzata la finestra di dialogo **Pubblica**.
7. In **endpoint connessione**selezionare l'endpoint per il cluster remoto Service Fabric Linux di destinazione.
![pubblica-applicazione]

<!--Image references-->
[Crea applicazione]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[Crea-servizio]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[compilazione-applicazione]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[Edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[pubblica-applicazione]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su [come iniziare a usare Service fabric con .NET Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
