---
title: Integrazione del gateway applicazione con gli endpoint di servizio-servizio app Azure | Microsoft Docs
description: Descrive il modo in cui il gateway applicazione si integra con app Azure servizio protetto con gli endpoint di servizio.
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
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980062"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Integrazione del gateway applicazione con gli endpoint di servizio
Sono disponibili tre varianti di servizio app che richiedono una configurazione leggermente diversa dell'integrazione con applicazione Azure gateway. Le varianti includono il servizio app normale, noto anche come ambiente del servizio app (ASE) multi-tenant, Internal Load Balancer (ILB) e ambiente del servizio app esterno. Questo articolo illustra come configurarlo con il servizio app (multi-tenant) e come discutere le considerazioni su ILB e l'ambiente del servizio app esterno.

## <a name="integration-with-app-service-multi-tenant"></a>Integrazione con il servizio app (multi-tenant)
Il servizio app (multi-tenant) ha un endpoint pubblico con connessione Internet. Usando gli [endpoint di servizio](../../virtual-network/virtual-network-service-endpoints-overview.md) è possibile consentire il traffico solo da una subnet specifica in una rete virtuale di Azure e bloccare tutti gli altri elementi. Nello scenario seguente verrà usata questa funzionalità per garantire che un'istanza del servizio app possa ricevere solo traffico da una specifica istanza del gateway applicazione.

![Integrazione del gateway applicazione con il servizio app](./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png)

Questa configurazione è costituita da due parti, oltre alla creazione del servizio app e del gateway applicazione. La prima parte consiste nell'abilitare gli endpoint di servizio nella subnet della rete virtuale in cui viene distribuito il gateway applicazione. Gli endpoint di servizio garantiranno che tutto il traffico di rete che lascia la subnet verso il servizio app verrà contrassegnato con l'ID subnet specifico. La seconda parte consiste nell'impostare una restrizione di accesso dell'app Web specifica, in modo da garantire che sia consentito solo il traffico contrassegnato con questo ID subnet specifico. È possibile configurarlo utilizzando diversi strumenti a seconda delle preferenze.

## <a name="using-azure-portal"></a>Uso del portale di Azure
Con portale di Azure, seguire quattro passaggi per eseguire il provisioning e configurare la configurazione. Se si dispone di risorse esistenti, è possibile ignorare i primi passaggi.
1. Creare un servizio app usando una delle guide introduttive nella documentazione del servizio app, ad esempio [.NET Core avvio rapido](../../app-service/app-service-web-get-started-dotnet.md)
2. Creare un gateway applicazione usando la [Guida introduttiva del portale](../../application-gateway/quick-create-portal.md), ma ignorare la sezione Aggiungi destinazioni backend.
3. Configurare il [servizio app come back-end nel gateway applicazione](../../application-gateway/configure-web-app-portal.md), ma ignorare la sezione limita accesso.
4. Infine, creare la [restrizione di accesso usando gli endpoint di servizio](../../app-service/app-service-ip-restrictions.md#service-endpoints).

È ora possibile accedere al servizio app tramite il gateway applicazione, ma se si tenta di accedere direttamente al servizio app, si dovrebbe ricevere un errore HTTP 403 che indica che il sito Web è stato arrestato.

![Integrazione del gateway applicazione con il servizio app](./media/app-gateway-with-service-endpoints/web-site-stopped.png)

## <a name="using-azure-resource-manager-template"></a>Uso del modello di Azure Resource Manager
Il [modello di distribuzione gestione risorse][template-app-gateway-app-service-complete] effettuerà il provisioning di uno scenario completo. Lo scenario è costituito da un'istanza del servizio app bloccata con gli endpoint di servizio e la restrizione di accesso per ricevere solo traffico dal gateway applicazione. Il modello include molte impostazioni predefinite intelligenti e postcorrezioni univoche aggiunte ai nomi delle risorse affinché risultino semplici. Per eseguire l'override, è necessario clonare il repository o scaricare il modello e modificarlo. 

Per applicare il modello, è possibile usare il pulsante Distribuisci in Azure disponibile nella descrizione del modello oppure usare PowerShell/CLI appropriato.

## <a name="using-azure-command-line-interface"></a>Uso dell'interfaccia della riga di comando di Azure
L' [esempio dell'interfaccia](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) della riga di comando di Azure eseguirà il provisioning di un servizio app bloccato con endpoint servizio e restrizione di accesso per ricevere solo traffico dal gateway applicazione. Se è necessario isolare il traffico a un servizio app esistente da un gateway applicazione esistente, il comando seguente è sufficiente.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

Nella configurazione predefinita, il comando garantirà l'installazione della configurazione dell'endpoint di servizio nella subnet e la restrizione di accesso nel servizio app.

## <a name="considerations-for-ilb-ase"></a>Considerazioni sull'ambiente del servizio app ILB
L'ambiente del servizio app ILB non è esposto a Internet e il traffico tra l'istanza e un gateway applicazione è quindi già isolato alla rete virtuale. La [Guida](../environment/integrate-with-application-gateway.md) dettagliata seguente configura un ambiente del servizio app ILB e lo integra con un gateway applicazione usando portale di Azure. 

Per assicurarsi che solo il traffico proveniente dalla subnet del gateway applicazione raggiunga l'ambiente del servizio app, è possibile configurare un gruppo di sicurezza di rete (NSG) che influisce su tutte le app Web nell'ambiente del servizio app. Per NSG, è possibile specificare l'intervallo IP della subnet e, facoltativamente, le porte (80/443). Assicurarsi di non eseguire l'override delle [regole NSG necessarie](../environment/network-info.md#network-security-groups) per il corretto funzionamento dell'ambiente del servizio app.

Per isolare il traffico a una singola app Web, è necessario usare le restrizioni di accesso basato su IP perché gli endpoint di servizio non funzioneranno per l'ambiente del servizio app. L'indirizzo IP deve essere l'indirizzo IP privato dell'istanza del gateway applicazione.

## <a name="considerations-for-external-ase"></a>Considerazioni sull'ambiente del servizio app esterno
L'ambiente del servizio app esterno ha un servizio di bilanciamento del carico pubblico come il servizio app multi-tenant. Gli endpoint di servizio non funzionano per l'ambiente del servizio app e questo è il motivo per cui è necessario usare restrizioni di accesso basato su IP usando l'indirizzo IP pubblico dell'istanza del gateway applicazione. Per creare un ambiente del servizio app esterno usando il portale di Azure, è possibile seguire questa [Guida introduttiva](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Modello di Azure Resource Manager per lo scenario completo"

## <a name="considerations-for-kuduscm-site"></a>Considerazioni per il sito Kudu/SCM
Il sito SCM, noto anche come Kudu, è un sito di amministrazione, disponibile per ogni app Web. Non è possibile eseguire il proxy inverso del sito SCM e probabilmente si vuole anche bloccarlo a singoli indirizzi IP o a una subnet specifica.

Se si desidera utilizzare le stesse restrizioni di accesso del sito principale, è possibile ereditare le impostazioni utilizzando il comando seguente.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Se si desidera impostare restrizioni di accesso individuali per il sito SCM, è possibile aggiungere restrizioni di accesso utilizzando il flag--SCM-site, come illustrato di seguito.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla ambiente del servizio app, vedere [ambiente del servizio app documentazione](https://docs.microsoft.com/azure/app-service/environment).

Per proteggere ulteriormente l'app Web, è possibile trovare informazioni su Web Application Firewall nel gateway applicazione nella [documentazione di Azure Web Application Firewall](../../web-application-firewall/ag/ag-overview.md).