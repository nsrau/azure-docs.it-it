---
title: Come distribuire il servizio di gestione dei certificati di OPC Vault - Azure Documenti Microsoft
description: Come distribuire il servizio di gestione dei certificati OPC Vault da zero.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 7ee186684b702a42335c6e1a7832cc5c761a69d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686947"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Creare e distribuire il servizio di gestione dei certificati OPC Vault

Questo articolo illustra come distribuire il servizio di gestione dei certificati di OPC Vault in Azure.This article explains how to deploy the OPC Vault certificate management service in Azure.

> [!NOTE]
> Per ulteriori informazioni, consultate il repository GitHub [OPC Vault](https://github.com/Azure/azure-iiot-opc-vault-service).

## <a name="prerequisites"></a>Prerequisiti

### <a name="install-required-software"></a>Installare il software necessario

Attualmente l'operazione di compilazione e distribuzione è limitata a Windows.
Gli esempi sono tutti scritti per .NET Standard di C,NET, che è necessario compilare il servizio ed esempi per la distribuzione.
Tutti gli strumenti necessari per .NET Standard vengono forniti con gli strumenti .NET Core. Vedere [Introduzione a .NET Core](https://docs.microsoft.com/dotnet/articles/core/getting-started).

1. [Installare .NET Core 2.1.][dotnet-install]
2. [Installare Docker][docker-url] (facoltativo, solo se è necessaria la build Docker locale).
4. Installare gli strumenti della riga di comando di [Azure per PowerShell.][powershell-install]
5. Iscriversi a una sottoscrizione di [Azure.][azure-free]

### <a name="clone-the-repository"></a>Clonare il repository

Se non l'hai ancora fatto, clona questo repository GitHub. Aprire un prompt dei comandi o un terminale ed eseguire quanto segue:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

In alternativa, è possibile clonare il repository direttamente in Visual Studio 2017.Alternatively, you can clone the repo directly in Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Compilare e distribuire il servizio Azure in WindowsBuild and deploy the Azure service on Windows

Uno script di PowerShell fornisce un modo semplice per distribuire il microservizio OPC Vault e l'applicazione.

1. Aprire una finestra di PowerShell nella radice del repository. 
3. Passare alla cartella `cd deploy`deploy .
3. Scegliere un `myResourceGroup` nome per il quale è improbabile che si verifichi un conflitto con altre pagine Web distribuite. Vedere la sezione "Nome del sito Web già in uso" più avanti in questo articolo.
5. Avviare la `.\deploy.ps1` distribuzione con l'installazione interattiva o immettere una riga di comando completa:Start the deployment with for interactive installation, or enter a full command line:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Se si prevede di sviluppare `-development 1` con questa distribuzione, aggiungere per abilitare l'interfaccia utente Swagger e distribuire le build di debug.
6. Seguire le istruzioni nello script per accedere alla sottoscrizione e fornire informazioni aggiuntive.
9. Dopo una corretta operazione di compilazione e distribuzione, verrà visualizzato il messaggio seguente:After a successful build and deploy operation, you should see the following message:
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > In caso di problemi, vedere la sezione "Risoluzione dei problemi di distribuzione" più avanti in questo articolo.

8. Aprire il browser preferito e aprire la pagina dell'applicazione:`https://myResourceGroup.azurewebsites.net`
8. Offri all'app Web e al microservizio OPC Vault alcuni minuti per riscaldarti dopo la distribuzione. La home page web potrebbe rimanere al primo utilizzo, per un massimo di un minuto, fino a quando non si ottengono le prime risposte.
11. Per dare un'occhiata all'API Swagger, apri:`https://myResourceGroup-service.azurewebsites.net`
13. Per avviare un server GDS `.\myResourceGroup-gds.cmd`locale con dotnet, avviare . Con Docker, `.\myResourceGroup-dockergds.cmd`avviare .

È possibile ridistribuire una compilazione con le stesse impostazioni. Tenere presente che un'operazione di questo tipo rinnova tutti i segreti dell'applicazione e potrebbe reimpostare alcune impostazioni nelle registrazioni dell'applicazione Azure Active Directory (Azure AD).

È anche possibile ridistribuire solo i file binari dell'app Web. Con il `-onlyBuild 1`parametro , i nuovi pacchetti zip del servizio e dell'app vengono distribuiti alle applicazioni Web.

Dopo la corretta distribuzione, è possibile iniziare a utilizzare i servizi. Consultate Gestire il servizio di [gestione dei certificati OPC Vault.](howto-opc-vault-manage.md)

## <a name="delete-the-services-from-the-subscription"></a>Eliminare i servizi dalla sottoscrizioneDelete the services from the subscription

Ecco come:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare al gruppo di risorse in cui è stato distribuito il servizio.
3. Scegliere **Elimina gruppo di risorse** e confermare.
4. Dopo un breve periodo di tempo, tutti i componenti del servizio distribuito vengono eliminati.
5. Passare a**Registrazioni**di **Azure Active Directory** > App .
6. Devono essere elencate tre registrazioni per ogni gruppo di risorse distribuito. Le registrazioni hanno i `resourcegroup-client` `resourcegroup-module`seguenti `resourcegroup-service`nomi: , , . Eliminare ogni registrazione separatamente.

Ora tutti i componenti distribuiti vengono rimossi.

## <a name="troubleshooting-deployment-failures"></a>Risoluzione degli errori di distribuzioneTroubleshooting deployment failures

### <a name="resource-group-name"></a>Nome del gruppo di risorse

Usare un nome di gruppo di risorse breve e semplice. Il nome viene utilizzato anche per denominare le risorse e il prefisso dell'URL del servizio. Di conseguenza, deve essere conforme ai requisiti di denominazione delle risorse.  

### <a name="website-name-already-in-use"></a>Nome del sito Web già in uso

È possibile che il nome del sito web sia già in uso. È necessario usare un nome di gruppo di risorse diverso. I nomi host utilizzati dallo script\/di distribuzione sono: https: /resourcegroupname.azurewebsites.net e https:\//resourgroupname-service.azurewebsites.net.
Altri nomi di servizi vengono creati mediante la combinazione di iseo con nome breve ed è improbabile che siano in conflitto con altri servizi.

### <a name="azure-ad-registration"></a>Registrazione di Azure AD 

Lo script di distribuzione tenta di registrare tre applicazioni Azure AD in Azure AD. A seconda delle autorizzazioni nel tenant di Azure AD selezionato, questa operazione potrebbe non riuscire. Sono disponibili due opzioni:

- Se si sceglie un tenant di Azure AD da un elenco di tenant, riavviare lo script e sceglierne uno diverso dall'elenco.
- In alternativa, distribuire un tenant di Azure AD privato in un'altra sottoscrizione. Riavviare lo script e scegliere di utilizzarlo.

## <a name="deployment-script-options"></a>Opzioni dello script di distribuzione

Lo script accetta i seguenti parametri:


```
-resourceGroupName
```

Può essere il nome di un gruppo di risorse esistente o nuovo.

```
-subscriptionId
```


Si tratta dell'ID sottoscrizione in cui verranno distribuite le risorse. È facoltativo.

```
-subscriptionName
```


In alternativa, è possibile utilizzare il nome della sottoscrizione.

```
-resourceGroupLocation
```


Si tratta di una posizione del gruppo di risorse. Se specificato, questo parametro tenta di creare un nuovo gruppo di risorse in questa posizione. Anche questo parametro è facoltativo.


```
-tenantId
```


Si tratta del tenant di Azure AD da usare. 

```
-development 0|1
```

Questo è per distribuire per lo sviluppo. Utilizzare la build di debug e impostare l'ambiente ASP.NET su Sviluppo.Use debug build, and set the ASP.NET environment to Development. Creare `.publishsettings` per l'importazione in Visual Studio 2017, per consentire la distribuzione diretta dell'app e del servizio. Anche questo parametro è facoltativo.

```
-onlyBuild 0|1
```

Si tratta di ricompilare e ridistribuire solo le app Web e di ricostruire i contenitori Docker. Anche questo parametro è facoltativo.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Passaggi successivi

Ora che hai imparato a distribuire OPC Vault da zero, puoi:

> [!div class="nextstepaction"]
> [Gestire il Vault OPC](howto-opc-vault-manage.md)
