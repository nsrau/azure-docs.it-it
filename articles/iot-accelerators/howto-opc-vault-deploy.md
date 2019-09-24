---
title: Come distribuire il servizio di gestione certificati dell'insieme di credenziali OPC-Azure | Microsoft Docs
description: Come distribuire il servizio di gestione certificati dell'insieme di credenziali OPC da zero.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f577059e1ebf70e3a9dfe9e538a9d3d49d7c8e96
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200004"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Compilare e distribuire il servizio di gestione certificati dell'insieme di credenziali OPC

Questo articolo illustra come distribuire il servizio di gestione certificati dell'insieme di credenziali OPC in Azure.

> [!NOTE]
> Per altre informazioni, vedere l'archivio dell'insieme di credenziali [OPC](https://github.com/Azure/azure-iiot-opc-vault-service)di GitHub.

## <a name="prerequisites"></a>Prerequisiti

### <a name="install-required-software"></a>Installare il software necessario

Attualmente, l'operazione di compilazione e distribuzione è limitata a Windows.
Gli esempi sono tutti scritti per C# .NET standard, che è necessario compilare il servizio ed esempi per la distribuzione.
Tutti gli strumenti necessari per .NET Standard sono disponibili con gli strumenti di .NET Core. Vedere [Introduzione a .NET Core](https://docs.microsoft.com/dotnet/articles/core/getting-started).

1. [Installare .NET Core 2.1 +][dotnet-install].
2. [Installare Docker][docker-url] (facoltativo, solo se la compilazione Docker locale è obbligatoria).
4. Installare gli [strumenti da riga di comando di Azure per PowerShell][powershell-install].
5. Iscriversi per una [sottoscrizione di Azure][azure-free].

### <a name="clone-the-repository"></a>Clonare il repository

Se non è ancora stato fatto, clonare questo repository GitHub. Aprire un prompt dei comandi o un terminale ed eseguire le operazioni seguenti:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

In alternativa, è possibile clonare il repository direttamente in Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Compilare e distribuire il servizio di Azure in Windows

Uno script di PowerShell fornisce un modo semplice per distribuire il microservizio dell'insieme di credenziali OPC e l'applicazione.

1. Aprire una finestra di PowerShell in corrispondenza della radice del repository. 
3. Passare alla cartella `cd deploy`deploy.
3. Scegliere un nome per `myResourceGroup` il quale è improbabile che si verifichi un conflitto con altre pagine Web distribuite. Vedere la sezione "nome del sito Web già in uso" più avanti in questo articolo.
5. Avviare la distribuzione con `.\deploy.ps1` per l'installazione interattiva oppure immettere una riga di comando completa:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Se si prevede di sviluppare con questa distribuzione, aggiungere `-development 1` per abilitare l'interfaccia utente di spavalderia e per distribuire le compilazioni di debug.
6. Seguire le istruzioni nello script per accedere alla sottoscrizione e per fornire informazioni aggiuntive.
9. Al termine di un'operazione di compilazione e distribuzione corretta, verrà visualizzato il messaggio seguente:
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
   > In caso di problemi, vedere la sezione "risoluzione degli errori di distribuzione" più avanti in questo articolo.

8. Aprire il browser preferito e aprire la pagina dell'applicazione:`https://myResourceGroup.azurewebsites.net`
8. Fornire all'app Web e al microservizio dell'insieme di credenziali OPC pochi minuti per scaldarsi dopo la distribuzione. Il home page Web potrebbe bloccarsi al primo utilizzo, fino a un minuto, fino a quando non si ottengono le prime risposte.
11. Per esaminare l'API spavalderia, aprire:`https://myResourceGroup-service.azurewebsites.net`
13. Per avviare un server GDS locale con dotnet, iniziare `.\myResourceGroup-gds.cmd`. Con Docker, avviare `.\myResourceGroup-dockergds.cmd`.

È possibile ridistribuire una compilazione con esattamente le stesse impostazioni. Tenere presente che tale operazione rinnova tutti i segreti dell'applicazione e può reimpostare alcune impostazioni nelle registrazioni dell'applicazione Azure Active Directory (Azure AD).

È anche possibile ridistribuire solo i file binari dell'app Web. Con il parametro `-onlyBuild 1`, i nuovi pacchetti ZIP del servizio e l'app vengono distribuiti alle applicazioni Web.

Una volta completata la distribuzione, è possibile iniziare a usare i servizi. Vedere [Manage the OPC Vault Certificate Management Service](howto-opc-vault-manage.md).

## <a name="delete-the-services-from-the-subscription"></a>Eliminare i servizi dalla sottoscrizione

Ecco come:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare al gruppo di risorse in cui è stato distribuito il servizio.
3. Scegliere **Elimina gruppo di risorse** e confermare.
4. Dopo un breve periodo di tempo, tutti i componenti del servizio distribuiti vengono eliminati.
5. Passare a **Azure Active Directory** > **registrazioni app**.
6. Per ogni gruppo di risorse distribuito dovrebbero essere elencate tre registrazioni. Le registrazioni hanno i nomi seguenti: `resourcegroup-client`, `resourcegroup-module`, `resourcegroup-service`. Eliminare ogni registrazione separatamente.

Tutti i componenti distribuiti verranno ora rimossi.

## <a name="troubleshooting-deployment-failures"></a>Risoluzione degli errori di distribuzione

### <a name="resource-group-name"></a>Nome gruppo di risorse

Usare un nome di gruppo di risorse breve e semplice. Il nome viene usato anche per assegnare un nome alle risorse e al prefisso dell'URL del servizio. Di conseguenza, deve essere conforme ai requisiti di denominazione delle risorse.  

### <a name="website-name-already-in-use"></a>Il nome del sito Web è già in uso

È possibile che il nome del sito Web sia già in uso. È necessario usare un nome di gruppo di risorse diverso. I nomi host usati dallo script di distribuzione sono: https://resourcegroupname.azurewebsites.net e. https://resourgroupname-service.azurewebsites.net
Altri nomi dei servizi sono compilati dalla combinazione degli hash dei nomi brevi ed è improbabile che si verifichino conflitti con altri servizi.

### <a name="azure-ad-registration"></a>Registrazione di Azure AD 

Lo script di distribuzione tenta di registrare tre applicazioni Azure AD in Azure AD. A seconda delle autorizzazioni nel tenant di Azure AD selezionato, questa operazione potrebbe non riuscire. Sono disponibili due opzioni:

- Se si sceglie un tenant di Azure AD da un elenco di tenant, riavviare lo script e sceglierne uno diverso dall'elenco.
- In alternativa, distribuire un tenant di Azure AD privato in un'altra sottoscrizione. Riavviare lo script e selezionare per usarlo.

## <a name="deployment-script-options"></a>Opzioni dello script di distribuzione

Lo script accetta i parametri seguenti:


```
-resourceGroupName
```

Può corrispondere al nome di un gruppo di risorse esistente o nuovo.

```
-subscriptionId
```


Si tratta dell'ID sottoscrizione in cui verranno distribuite le risorse. È facoltativo.

```
-subscriptionName
```


In alternativa, è possibile usare il nome della sottoscrizione.

```
-resourceGroupLocation
```


Si tratta di un percorso del gruppo di risorse. Se specificato, questo parametro tenta di creare un nuovo gruppo di risorse in questa posizione. Questo parametro è anche facoltativo.


```
-tenantId
```


Si tratta del tenant Azure AD da usare. 

```
-development 0|1
```

Questa operazione viene distribuita per lo sviluppo. Usare la build di debug e impostare l'ambiente ASP.NET su Development. Creare `.publishsettings` per l'importazione in Visual Studio 2017, per consentire la distribuzione diretta dell'app e del servizio. Questo parametro è anche facoltativo.

```
-onlyBuild 0|1
```

Questa operazione consente di ricompilare e ridistribuire solo le app Web e di ricompilare i contenitori docker. Questo parametro è anche facoltativo.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come distribuire un insieme di credenziali OPC da zero, è possibile:

> [!div class="nextstepaction"]
> [Gestisci insieme di credenziali OPC](howto-opc-vault-manage.md)
