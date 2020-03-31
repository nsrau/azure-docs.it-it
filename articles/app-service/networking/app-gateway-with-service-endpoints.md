---
title: Integrazione del gateway applicazione con gli endpoint del servizio - Servizio app di Azure Documenti Microsoft
description: Descrive come il gateway applicazione si integra con il servizio app di Azure protetto con gli endpoint del servizio.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 5e32baa10e98f0f57a861f8cebfb7506ad615631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980062"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Integrazione del gateway applicazione con gli endpoint del servizioApplication Gateway integration with service endpoints
Esistono tre varianti del servizio app che richiedono una configurazione leggermente diversa dell'integrazione con il gateway applicazione di Azure.There are three variations of App Service that require slightly different configuration of the integration with Azure Application Gateway. Le varianti includono il servizio app regolare, noto anche come multi-tenant, Internal Load Balancer (ILB) App Service Environment (ASE) e External ASE. Questo articolo illustra come configurarlo con il servizio app (multi-tenant) e discuterà le considerazioni su ILB e l'ambiente del servizio app esterno.

## <a name="integration-with-app-service-multi-tenant"></a>Integrazione con il servizio app (multi-tenant)
Il servizio app (multi-tenant) dispone di un endpoint pubblico con connessione Internet.App Service (multi-tenant) has a public internet facing endpoint. Usando [gli endpoint](../../virtual-network/virtual-network-service-endpoints-overview.md) di servizio è possibile consentire il traffico solo da una subnet specifica all'interno di una rete virtuale di Azure e bloccare tutto il resto. Nello scenario seguente useremo questa funzionalità per garantire che un'istanza del servizio app possa ricevere traffico solo da un'istanza del gateway applicazione specifica.

![Integrazione del gateway applicazione con il servizio appApplication Gateway integration with App Service](./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png)

Questa configurazione è di due parti oltre alla creazione del servizio app e del gateway applicazione. La prima parte consiste nell'abilitare gli endpoint del servizio nella subnet della rete virtuale in cui viene distribuito il gateway applicazione. Gli endpoint di servizio garantiranno che tutto il traffico di rete che esce dalla subnet verso il servizio app verrà contrassegnato con l'ID subnet specifico. La seconda parte consiste nell'impostare una restrizione di accesso dell'app Web specifica per garantire che sia consentito solo il traffico contrassegnato con questo ID subnet specifico. È possibile configurarlo utilizzando strumenti diversi a seconda delle preferenze.

## <a name="using-azure-portal"></a>Uso del portale di Azure
Con il portale di Azure, seguire quattro passaggi per eseguire il provisioning e configurare la configurazione. Se si dispone di risorse esistenti, è possibile ignorare i primi passaggi.
1. Creare un servizio app usando una delle guide introduttive nella documentazione del servizio app, ad esempio Guida introduttiva di .Net CoreCreate an App Service using one of the Quickstarts in the App Service documentation, for example [.Net Core Quickstart](../../app-service/app-service-web-get-started-dotnet.md)
2. Creare un gateway applicazione utilizzando la [Guida introduttiva](../../application-gateway/quick-create-portal.md)al portale , ma ignorare la sezione Aggiungere destinazioni back-end .
3. Configurare il [servizio app come back-end nel gateway applicazione,](../../application-gateway/configure-web-app-portal.md)ma ignorare la sezione Limita accesso.
4. Creare infine la restrizione di accesso usando gli endpoint del [servizio.](../../app-service/app-service-ip-restrictions.md#service-endpoints)

È ora possibile accedere al servizio app tramite il gateway applicazione, ma se si tenta di accedere direttamente al servizio app, verrà visualizzato un errore HTTP 403 che indica che il sito Web è stato arrestato.

![Integrazione del gateway applicazione con il servizio appApplication Gateway integration with App Service](./media/app-gateway-with-service-endpoints/web-site-stopped.png)

## <a name="using-azure-resource-manager-template"></a>Uso del modello di Azure Resource Manager
Il modello di distribuzione di [Resource Manager][template-app-gateway-app-service-complete] eseguirà il provisioning di uno scenario completo. Lo scenario è costituito da un'istanza del servizio app bloccata con endpoint di servizio e restrizioni di accesso per ricevere solo il traffico dal gateway applicazione. Il modello include molte impostazioni predefinite intelligenti e suffissi univoci aggiunti ai nomi delle risorse affinché sia semplice. Per ignorarli, dovrai clonare il repository o scaricare il modello e modificarlo. 

Per applicare il modello è possibile usare il pulsante Distribuisci in Azure disponibile nella descrizione del modello oppure PowerShell/CLI appropriato.

## <a name="using-azure-command-line-interface"></a>Uso dell'interfaccia della riga di comando di AzureUsing Azure Command Line Interface
[L'esempio dell'interfaccia della riga di](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) comando di Azure eseguirà il provisioning di un servizio app bloccato con gli endpoint del servizio e la restrizione di accesso per ricevere solo il traffico dal gateway applicazione. Se è necessario solo isolare il traffico verso un servizio app esistente da un gateway applicazione esistente, è sufficiente il comando seguente.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

Nella configurazione predefinita, il comando garantisce sia la configurazione dell'endpoint del servizio nella subnet che la restrizione di accesso nel servizio app.

## <a name="considerations-for-ilb-ase"></a>Considerazioni per il numero ASE ILBConsiderations for ILB ASE
L'ambiente del servizio app ILB non è esposto a Internet e il traffico tra l'istanza e un gateway applicazione è pertanto già isolato nella rete virtuale. La [guida how-to](../environment/integrate-with-application-gateway.md) seguente configura un'applicazione del servizio app ILB e la integra con un gateway applicazione tramite il portale di Azure.The following how-to guide configures an ILB ASE and integrates it with an Application Gateway using Azure portal. 

Se si vuole garantire che solo il traffico proveniente dalla subnet del gateway applicazione raggiunga l'app del servizio app, è possibile configurare un gruppo di sicurezza di rete (NSG) che interessa tutte le app Web nell'app del servizio app. Per il gruppo di sicurezza di rete, è possibile specificare l'intervallo IP della subnet e, facoltativamente, le porte (80/443). Assicurarsi di non eseguire l'override delle [regole del gruppo](../environment/network-info.md#network-security-groups) di sicurezza di rete necessarie per il corretto funzionamento dell'ASE.

Per isolare il traffico verso una singola app Web, è necessario usare restrizioni di accesso basate su IP poiché gli endpoint del servizio non funzioneranno per l'app del servizio app. L'indirizzo IP deve essere l'indirizzo IP privato dell'istanza del gateway applicazione.

## <a name="considerations-for-external-ase"></a>Considerazioni per l'ambiente del database esternoConsiderations for External ASE
L'ambiente del servizio app esterno include un servizio di bilanciamento del carico pubblico, ad esempio il servizio app multi-tenant. Gli endpoint del servizio non funzionano per l'app ed è per questo che dovrai usare restrizioni di accesso basate su IP usando l'indirizzo IP pubblico dell'istanza del gateway applicazione. Per creare un'app esterna usando il portale di Azure, è possibile seguire questa [guida introduttivaTo](../environment/create-external-ase.md) create an External ASE using the Azure portal, you can follow this Quickstart

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Modello di Azure Resource Manager per uno scenario completoAzure Resource Manager template for complete scenario"

## <a name="considerations-for-kuduscm-site"></a>Considerazioni per il sito kudu/scm
Il sito scm, noto anche come kudu, è un sito di amministrazione, che esiste per ogni app web. Non è possibile invertire il proxy del sito scm e molto probabilmente si desidera bloccarlo anche su singoli indirizzi IP o una subnet specifica.

Se si desidera utilizzare le stesse restrizioni di accesso del sito principale, è possibile ereditare le impostazioni utilizzando il comando seguente.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Se si desidera impostare singole restrizioni di accesso per il sito scm, è possibile aggiungere restrizioni di accesso utilizzando il flag --scm-site come illustrato di seguito.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'ambiente del servizio app, vedere la documentazione relativa [all'ambiente del servizio app.](https://docs.microsoft.com/azure/app-service/environment)

Per proteggere ulteriormente l'app Web, le informazioni su Web Application Firewall nel gateway applicazione sono disponibili nella documentazione di Firewall applicazione Web di [Azure.](../../web-application-firewall/ag/ag-overview.md)