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
ms.openlocfilehash: 1625f0e6f9bfe8297cae2770e63107bf4f19f95e
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012991"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Compilare e distribuire il servizio di gestione certificati dell'insieme di credenziali OPC

Questo articolo illustra come distribuire il servizio di gestione certificati dell'insieme di credenziali OPC in Azure.

> [!NOTE]
> Per altre informazioni sui dettagli e le istruzioni per la distribuzione, vedere l'archivio dell'insieme di credenziali [OPC](https://github.com/Azure/azure-iiot-opc-vault-service)di GitHub.

## <a name="prerequisites"></a>Prerequisiti

### <a name="install-required-software"></a>Installare il software necessario

Attualmente, l'operazione di compilazione e distribuzione è limitata a Windows.
Gli esempi sono tutti scritti per C# .NET standard, necessari per compilare il servizio ed esempi per la distribuzione.
Tutti gli strumenti necessari per .NET standard sono disponibili con gli strumenti .NET Core. Per informazioni sugli elementi necessari, vedere [qui](https://docs.microsoft.com/dotnet/articles/core/getting-started) .

1. [Installare .NET Core 2.1 +][dotnet-install].
2. [Installare Docker][docker-url] (facoltativo, solo se la compilazione Docker locale è obbligatoria).
4. Installare gli [strumenti da riga di comando di Azure per PowerShell][powershell-install].
5. Iscriversi per una [sottoscrizione di Azure][azure-free].

### <a name="clone-the-repository"></a>Clonare il repository

Se non è ancora stato fatto, clonare questo repository GitHub.  Aprire un prompt dei comandi o un terminale ed eseguire:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

in alternativa, clonare il repository direttamente in Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Compilare e distribuire il servizio di Azure in Windows

Uno script di PowerShell fornisce un modo semplice per distribuire il microservizio dell'insieme di credenziali OPC e l'applicazione.<br>

1. Aprire una finestra di PowerShell in corrispondenza della radice del repository. 
3. Passa alla cartella Deploy`cd deploy`
3. Scegliere un nome per `myResourceGroup` il quale è improbabile che si verifichi un conflitto con altre pagine Web distribuite. Di [seguito](#website-name-already-in-use) viene illustrato come scegliere i nomi delle pagine Web in base al nome del gruppo di risorse.
5. Avviare la distribuzione con `.\deploy.ps1` per l'installazione interattiva<br>
in alternativa, immettere una riga di comando completa:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Se si prevede di sviluppare con questa distribuzione, aggiungere `-development 1` per abilitare l'interfaccia utente di spavalderia e distribuire le compilazioni di debug.
6. Seguire le istruzioni nello script per accedere alla sottoscrizione e fornire informazioni aggiuntive.
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

Se si verificano problemi, attenersi alla procedura [riportata di seguito](#troubleshooting-deployment-failures).

8. Aprire il browser preferito e aprire la pagina dell'applicazione:`https://myResourceGroup.azurewebsites.net`
8. Fornire all'app Web e al microservizio dell'insieme di credenziali OPC pochi minuti per scaldarsi dopo la distribuzione. È possibile che il home page Web si blocchi al primo utilizzo per un massimo di un minuto fino a quando non si ottengono le prime risposte.
11. Per esaminare l'API di spavalderia, aprire:`https://myResourceGroup-service.azurewebsites.net`
13. Per avviare un server GDS locale con DotNet Start `.\myResourceGroup-gds.cmd` o con Docker Start `.\myResourceGroup-dockergds.cmd`.

Come margine, è possibile ridistribuire una compilazione con esattamente le stesse impostazioni. Tenere presente che tale operazione rinnova tutti i segreti dell'applicazione e può reimpostare alcune impostazioni nelle registrazioni dell'applicazione Azure Active Directory (Azure AD).

È anche possibile ridistribuire solo i file binari dell'app Web. Con il parametro `-onlyBuild 1` i nuovi pacchetti ZIP del servizio e l'app vengono distribuiti alle applicazioni Web.

Una volta completata la distribuzione, è possibile iniziare a usare i servizi: [Come gestire il servizio di gestione certificati dell'insieme di credenziali OPC](howto-opc-vault-manage.md)

## <a name="delete-the-services-from-the-subscription"></a>Eliminare i servizi dalla sottoscrizione

1. Accedere al portale di Azure: `https://portal.azure.com`.
2. Passare al gruppo di risorse in cui è stato distribuito il servizio.
3. Selezionare `Delete resource group` e confermare.
4. Dopo un breve periodo di tempo, tutti i componenti del servizio distribuiti vengono eliminati.
5. Passare ora a `Azure Active Directory/App registrations`.
6. Per ogni gruppo di risorse distribuito dovrebbero essere elencate tre registrazioni con i nomi seguenti: `resourcegroup-client`, `resourcegroup-module`, `resourcegroup-service`.
Ogni registrazione deve essere eliminata separatamente.
7. Tutti i componenti distribuiti verranno ora rimossi.

## <a name="troubleshooting-deployment-failures"></a>Risoluzione degli errori di distribuzione

### <a name="resource-group-name"></a>Nome del gruppo di risorse

Assicurarsi di usare un nome di gruppo di risorse breve e semplice.  Il nome viene usato anche per denominare le risorse e il prefisso dell'URL del servizio e, di conseguenza, deve essere conforme ai requisiti di denominazione delle risorse.  

### <a name="website-name-already-in-use"></a>Il nome del sito Web è già in uso

È possibile che il nome del sito Web sia già in uso.  Se si è eseguito questo errore, è necessario usare un nome di gruppo di risorse diverso. I nomi host usati dallo script di distribuzione sono: https://resourcegroupname.azurewebsites.net e. https://resourgroupname-service.azurewebsites.net
Gli altri nomi dei servizi sono compilati dalla combinazione degli hash dei nomi brevi ed è improbabile che si verifichino conflitti con altri servizi.

### <a name="azure-active-directory-azure-ad-registration"></a>Registrazione di Azure Active Directory (Azure AD) 

Lo script di distribuzione tenta di registrare tre applicazioni Azure AD in Azure Active Directory.  
A seconda delle autorizzazioni nel tenant di Azure AD selezionato, questa operazione potrebbe non riuscire.   Sono disponibili due opzioni:

1. Se si sceglie un tenant di Azure AD da un elenco di tenant, riavviare lo script e sceglierne uno diverso dall'elenco.
2. In alternativa, distribuire un tenant di Azure AD privato in un'altra sottoscrizione, riavviare lo script e selezionare per usarlo.

## <a name="deployment-script-options"></a>Opzioni dello script di distribuzione

Lo script accetta i parametri seguenti:


```
-resourceGroupName
```

Può essere il nome di un gruppo di risorse esistente o nuovo.

```
-subscriptionId
```


Facoltativo, ID sottoscrizione in cui verranno distribuite le risorse.

```
-subscriptionName
```


In alternativa, il nome della sottoscrizione.

```
-resourceGroupLocation
```


Facoltativo, percorso del gruppo di risorse. Se specificato, tenterà di creare un nuovo gruppo di risorse in questa posizione.


```
-tenantId
```


Azure AD tenant da utilizzare. 

```
-development 0|1
```

Facoltativo, per la distribuzione per lo sviluppo. Usare la build di debug e impostare l'ambiente ASP.Net su Development. Creare ". publishsettings" per l'importazione in Visual Studio 2017 per consentire la distribuzione diretta dell'app e del servizio.

```
-onlyBuild 0|1
```

Facoltativo, per ricompilare e ridistribuire solo le app Web e per ricompilare i contenitori docker.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come distribuire un insieme di credenziali OPC da zero, ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Gestisci insieme di credenziali OPC](howto-opc-vault-manage.md)
