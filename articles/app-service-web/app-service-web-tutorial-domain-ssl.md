---
title: Aggiungere un dominio personalizzato e un certificato SSL a un'app Web di Azure | Microsoft Docs
description: "Informazioni su come preparare l'app Web di Azure per l'ambiente di produzione aggiungendo il marchio della società. Eseguire il mapping del nome di dominio personalizzato (dominio personale) all'app Web e proteggerlo con un certificato SSL personalizzato."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 03/29/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 535a7dbe24a1badc8539b61d34c09bdeda41ad40
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017

---
# <a name="add-custom-domain-and-ssl-to-an-azure-web-app"></a>Aggiungere un dominio personalizzato e un certificato SSL a un'app Web di Azure

Questa esercitazione illustra come eseguire rapidamente il mapping di un nome di dominio personalizzato all'app Web di Azure e quindi garantirne la sicurezza con un certificato SSL personalizzato. 

## <a name="before-you-begin"></a>Prima di iniziare

Prima di eseguire l'esempio, installare l'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) in locale.

È anche necessario l'accesso amministrativo alla pagina di configurazione DNS per il provider di dominio pertinente. Per aggiungere, ad esempio, `www.contoso.com`, è necessario essere in grado di configurare voci DNS per `contoso.com`.

Infine, sono necessari un file PFX valido _e_ la relativa password per il certificato SSL da caricare e di cui eseguire il binding. Il certificato SSL deve essere configurato per proteggere il nome di dominio personalizzato desiderato. Nell'esempio precedente il certificato SSL deve proteggere `www.contoso.com`. 

## <a name="step-1---create-an-azure-web-app"></a>Passaggio 1 - Creare un'app Web di Azure

### <a name="log-in-to-azure"></a>Accedere ad Azure

Verrà ora usata l'interfaccia della riga di comando di Azure 2.0 in una finestra del terminale per creare le risorse necessarie per ospitare l'app Node.js in Azure.  Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate. 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>Creare un gruppo di risorse   
Creare un gruppo di risorse con [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite e gestite risorse di Azure come app Web, database e account di archiviazione. 

```azurecli
az group create --name myResourceGroup --location westeurope 
```

Per visualizzare i possibili valori utilizzabili per `---location`, usare il comando `az appservice list-locations` dell'interfaccia della riga di comando di Azure.

## <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

Creare un piano di servizio app con il comando [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

L'esempio seguente crea un piano di servizio app denominato `myAppServicePlan` usando il piano tariffario **Basic**.

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1

Dopo aver creato il piano di servizio app, l'interfaccia della riga di comando di Azure mostra informazioni simili a quelle dell'esempio seguente. 

```json 
{ 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "kind": "app", 
    "location": "West Europe", 
    "sku": { 
    "capacity": 1, 
    "family": "B", 
    "name": "B1", 
    "tier": "Basic" 
    }, 
    "status": "Ready", 
    "type": "Microsoft.Web/serverfarms" 
} 
``` 

## <a name="create-a-web-app"></a>Creare un'app Web

Ora che il piano di servizio app è stato creato, creare un'app Web nel piano `myAppServicePlan`. L'app Web fornisce uno spazio di hosting per la distribuzione del codice e un URL per visualizzare l'applicazione distribuita. Usare il comando [az appservice web create](/cli/azure/appservice/web#create) per creare l'app Web. 

Nel comando seguente sostituire il segnaposto `<app_name>` con il nome univoco della propria app. Questo nome univoco verrà usato come parte del nome di dominio predefinito per l'app Web, quindi è necessario che il nome sia univoco in tutte le app in Azure. In un secondo momento è possibile eseguire il mapping di qualsiasi voce DNS personalizzata all'app Web prima di esporla agli utenti. 

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan 
```

Dopo aver creato l'app Web, l'interfaccia della riga di comando di Azure mostra informazioni simili a quelle dell'esempio seguente. 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

Nell'output JSON `defaultHostName` indica il nome di dominio predefinito dell'app Web. Nel browser passare a questo indirizzo.

```
http://<app_name>.azurewebsites.net 
``` 
 
![App Web creata nel servizio app](media/app-service-web-tutorial-domain-ssl/web-app-created.png)  

## <a name="step-2---configure-dns-mapping"></a>Passaggio 2 - Configurare il mapping DNS

In questo passaggio si aggiunge un mapping da un dominio personalizzato al nome di dominio predefinito dell'app Web, `<app_name>.azurewebsites.net`. In genere si esegue questo passaggio nel sito Web del provider del dominio. Il sito Web di ogni registrar di dominio è leggermente diverso, quindi è consigliabile vedere la documentazione del provider. Di seguito sono tuttavia indicate alcune linee guida generali. 

### <a name="navigate-to-to-dns-management-page"></a>Passare alla pagina di gestione DNS

Accedere prima di tutto al sito Web del registrar del dominio.  

Individuare quindi la pagina relativa alla gestione dei record DNS. Individuare collegamenti o aree del sito denominate **Domain Name**, **DNS** o **Name Server Management**. Spesso è possibile trovare il collegamento visualizzando le informazioni dell'account, cercando una voce simile a **Domini personali**.

Una volta trovata la pagina, cercare un collegamento che consenta di aggiungere o modificare i record DNS. Potrebbe trattarsi di un collegamento a un **file di zona** o a **record DNS** oppure di un collegamento a una **configurazione avanzata**.

### <a name="create-a-cname-record"></a>Creare un record CNAME

Aggiungere un record CNAME che esegue il mapping del nome di sottodominio desiderato al nome di dominio predefinito dell'app Web (`<app_name>.azurewebsites.net`, dove `<app_name>` è il nome univoco dell'app).

Per l'esempio relativo a `www.contoso.com`, si crea un record CNAME che esegue il mapping del nome host `www` a `<app_name>.azurewebsites.net`.

## <a name="step-3---configure-the-custom-domain-on-your-web-app"></a>Passaggio 3 - Configurare il dominio personalizzato nell'app Web

Dopo aver configurato il mapping del nome host nel sito Web del provider di dominio, si è pronti per configurare il dominio personalizzato nell'app Web. Usare il comando [az appservice web config hostname add](/cli/azure/appservice/web/config/hostname#add) per aggiungere questa configurazione. 

Nel comando seguente sostituire `<app_name>` con il nome univoco dell'app e <your_custom_domain> con il nome di dominio personalizzato completo (ad esempio `www.contoso.com`). 

```azurecli
az appservice web config hostname add --webapp <app_name> --resource-group myResourceGroup --name <your_custom_domain>
```

Il dominio personalizzato è ora completamente mappato all'app Web. Nel browser passare al nome di dominio personalizzato. Ad esempio:

```
http://www.contoso.com 
``` 

![App Web creata nel servizio app](media/app-service-web-tutorial-domain-ssl/web-app-custom-domain.png)  

## <a name="step-4---bind-a-custom-ssl-certificate-to-your-web-app"></a>Passaggio 4 - Eseguire il binding di un certificato SSL personalizzato all'app Web

A questo punto si ha un'app web di Azure con il nome di dominio desiderato nella barra degli indirizzi del browser. Se tuttavia si passa a `https://<your_custom_domain>`, si verifica un errore di certificato. 

![App Web creata nel servizio app](media/app-service-web-tutorial-domain-ssl/web-app-cert-error.png)  

Questo errore si verifica perché l'app Web non ha ancora un binding a un certificato SSL corrispondente al nome del dominio personalizzato. Non viene invece generato un errore se si passa a `https://<app_name>.azurewebsites.net`. Questo accade perché l'app, come tutte le app di Servizio app di Azure, è protetta con il certificato SSL per il dominio con caratteri jolly `*.azurewebsites.net` per impostazione predefinita. 

Per accedere all'app Web dal nome di dominio personalizzato, è necessario eseguire il binding del certificato SSL per il dominio personalizzato all'app Web. Questa operazione verrà eseguita in questo passaggio. 

### <a name="upload-the-ssl-certificate"></a>Caricare il certificato SSL

Caricare il certificato SSL per il dominio personalizzato nell'app Web usando il comando [az appservice web config ssl upload](/cli/azure/appservice/web/config/ssl#upload).

Nel comando seguente sostituire `<app_name>` con il nome univoco dell'app, `<path_to_ptx_file>` con il percorso del file PFX e `<password>` con la password del certificato. 

```azurecli
az appservice web config ssl upload --name <app_name> --resource-group myResourceGroup --certificate-file <path_to_pfx_file> --certificate-password <password> 
```

Dopo aver caricato il certificato, l'interfaccia della riga di comando di Azure mostra informazioni simili a quelle dell'esempio seguente:

```json
{
  "cerBlob": null,
  "expirationDate": "2018-03-29T14:12:57+00:00",
  "friendlyName": "",
  "hostNames": [
    "www.contoso.com"
  ],
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/cert
ificates/9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "issueDate": "2017-03-29T14:12:57+00:00",
  "issuer": "www.contoso.com",
  "keyVaultId": null,
  "keyVaultSecretName": null,
  "keyVaultSecretStatus": "Initialized",
  "kind": null,
  "location": "West Europe",
  "name": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "password": null,
 "pfxBlob": null,
  "publicKeyHash": null,
  "resourceGroup": "myResourceGroup",
  "selfLink": null,
  "serverFarmId": null,
  "siteName": null,
  "subjectName": "www.contoso.com",
  "tags": null,
  "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00",
  "type": "Microsoft.Web/certificates",
  "valid": null
}
```

Nell'output JSON `thumbprint` indica l'identificazione personale del certificato caricato. Copiare il valore per il passaggio successivo.

### <a name="bind-the-uploaded-ssl-certificate-to-the-web-app"></a>Eseguire il binding del certificato SSL caricato all'app Web

L'app web ha ora il nome di dominio personalizzato desiderato, oltre che un certificato SSL che protegge il dominio personalizzato. L'unica operazione che rimane da fare è eseguire il binding del certificato caricato all'app Web. A tale scopo, usare il comando[az appservice web config ssl bind](/cli/azure/appservice/web/config/ssl#bind).

Nel comando seguente sostituire `<app_name>` con il nome univoco dell'app e `<thumbprint-from-previous-output>` con l'identificazione personale del certificato ottenuta dal comando precedente. 

az appservice web config ssl bind --name <app_name> --resource-group myResourceGroup --certificate-thumbprint <thumbprint-from-previous-output> --ssl-type SNI

Dopo aver eseguito il binding del certificato all'app Web, l'interfaccia della riga di comando di Azure mostra informazioni simili a quelle dell'esempio seguente:

{ "availabilityState": "Normal", "clientAffinityEnabled": true, "clientCertEnabled": false, "cloningInfo": null, "containerSize": 0, "dailyMemoryTimeQuota": 0, "defaultHostName": "<app_name>.azurewebsites.net", "enabled": true, "enabledHostNames": [ "www.contoso.com", "<app_name>.azurewebsites.net", "<app_name>.scm.azurewebsites.net" ], "gatewaySiteName": null, "hostNameSslStates": [ { "hostType": "Standard", "name": "<app_name>.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Repository", "name": "<app_name>.scm.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Standard", "name": "www.contoso.com", "sslState": "SniEnabled", "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00", "toUpdate": null, "virtualIp": null } ], "hostNames": [ "www.contoso.com", "<app_name>.azurewebsites.net" ], "hostNamesDisabled": false, "hostingEnvironmentProfile": null, "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/site s/<app_name>", "isDefaultContainer": null, "kind": "WebApp", "lastModifiedTimeUtc": "2017-03-29T14:36:18.803333", "location": "West Europe", "maxNumberOfWorkers": null, "microService": "false", "name": "<app_name>", "outboundIpAddresses": "13.94.143.57,13.94.136.57,40.68.199.146,13.94.138.55,13.94.140.1", "premiumAppDeployed": null, "repositorySiteName": "<app_name>", "reserved": false, "resourceGroup": "myResourceGroup", "scmSiteAlsoStopped": false, "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsof t.Web/serverfarms/myAppServicePlan", "siteConfig": null, "slotSwapStatus": null, "state": "Running", "suspendedTill": null, "tags": null, "targetSwapSlot": null, "trafficManagerHostNames": null, "type": "Microsoft.Web/sites", "usageState": "Normal" }

Nel browser passare all'endpoint HTTPS del nome di dominio personalizzato. Ad esempio:

```
https://www.contoso.com 
``` 

![App Web creata nel servizio app](media/app-service-web-tutorial-domain-ssl/web-app-ssl-success.png)  

## <a name="more-resources"></a>Altre risorse

[Acquistare e configurare un nome di dominio personalizzato in Servizio app di Azure](custom-dns-web-site-buydomains-web-app.md)
[Acquistare e configurare un certificato SSL per il servizio app di Azure](web-sites-purchase-ssl-web-site.md)

