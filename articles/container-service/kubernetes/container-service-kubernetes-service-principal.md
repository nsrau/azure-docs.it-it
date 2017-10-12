---
title: "Entità servizio per cluster Azure Kubernetes | Documentazione Microsoft"
description: "Creare e gestire un'entità servizio di Azure Active Directory per un cluster Kubernetes nel servizio contenitore di Azure"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 14975454cbc0afcfbdbd3aa6b52983be4d4b1785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-an-azure-ad-service-principal-for-a-kubernetes-cluster-in-container-service"></a>Configurare un'entità servizio di Azure AD per un cluster Kubernetes nel servizio contenitore


Un cluster Kubernetes richiede un'[entità servizio di Azure Active Directory](../../active-directory/develop/active-directory-application-objects.md) nel servizio contenitore di Azure per l'interazione con le API di Azure. L'entità servizio è necessaria per la gestione dinamica di risorse quali le [route definite dall'utente](../../virtual-network/virtual-networks-udr-overview.md) e [Azure Load Balancer di livello 4](../../load-balancer/load-balancer-overview.md).


Questo articolo illustra le diverse opzioni disponibili per configurare un'entità servizio per il cluster Kubernetes. Se, ad esempio, l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) è già stata installata e configurata, è possibile eseguire il comando [`az acs create`](/cli/azure/acs#create) per creare il cluster Kubernetes e l'entità servizio contemporaneamente.


## <a name="requirements-for-the-service-principal"></a>Requisiti per l'entità servizio

È possibile usare un'entità servizio di Azure AD esistente che soddisfi i requisiti seguenti oppure crearne una nuova.

* **Ambito**: la sottoscrizione usata per distribuire il cluster.

* **Ruolo**: **Collaboratore**.

* **Segreto client**: deve essere una password. Non è attualmente possibile usare un'entità servizio configurata per l'autenticazione del certificato.

> [!IMPORTANT]
> Per creare un'entità servizio sono necessarie autorizzazioni sufficienti per registrare un'applicazione con il tenant di Azure AD e assegnare l'applicazione a un ruolo nella sottoscrizione. È possibile verificare se si hanno a disposizione le autorizzazioni necessarie [nel portale](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions).
>

## <a name="option-1-create-a-service-principal-in-azure-ad"></a>Opzione 1: creare un'entità servizio in Azure AD

Azure mette a disposizione diversi metodi per creare un'entità servizio di Azure AD prima di distribuire il cluster Kubernetes.

I comandi di esempio seguenti illustrano come eseguire questa operazione con l'[interfaccia della riga di comando Azure 2.0](../../azure-resource-manager/resource-group-authenticate-service-principal-cli.md). In alternativa è possibile creare un'entità servizio usando [Azure PowerShell](../../azure-resource-manager/resource-group-authenticate-service-principal.md), il [portale](../../azure-resource-manager/resource-group-create-service-principal-portal.md) o altri metodi.

```azurecli
az login

az account set --subscription "mySubscriptionID"

az group create --name "myResourceGroup" --location "westus"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

L'output è simile al seguente (visualizzato con alcune modifiche):

![Creare un’entità servizio](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

Sono evidenziati l'**ID client** (`appId`) e il **segreto client** (`password`) usati come parametri dell'entità servizio per la distribuzione del cluster.


### <a name="specify-service-principal-when-creating-the-kubernetes-cluster"></a>Specificare l'entità servizio quando si crea il cluster Kubernetes

Specificare l'**ID client**, anche chiamato `appId` ovvero ID applicazione, e il **segreto client** (`password`) di un'entità servizio esistente come parametri durante la creazione del cluster Kubernetes. Verificare che l'entità servizio soddisfi i requisiti illustrati all'inizio di questo articolo.

È possibile specificare questi parametri durante la distribuzione del cluster Kubernetes usando l'[interfaccia della riga di comando di Azure 2.0](container-service-kubernetes-walkthrough.md), il [portale di Azure](../dcos-swarm/container-service-deployment.md) o altri metodi.

>[!TIP]
>Quando si specifica l'**ID client**, assicurarsi di usare il valore `appId`, non `ObjectId`, dell'entità servizio.
>

L'esempio seguente illustra un modo per passare i parametri con l'interfaccia della riga di comando di Azure 2.0. Questo esempio usa il [modello di avvio rapido di Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

1. [Scaricare](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json) il file di parametri del modello `azuredeploy.parameters.json` da GitHub.

2. Per specificare l'entità servizio, immettere i valori per `servicePrincipalClientId` e `servicePrincipalClientSecret` nel file. È anche necessario specificare valori personalizzati per `dnsNamePrefix` e `sshRSAPublicKey`, che indica la chiave pubblica SSH per l'accesso al cluster. Salvare il file.

    ![Passare i parametri dell'entità servizio](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. Eseguire il comando seguente, usando `--parameters` per impostare il percorso per il file azuredeploy.parameters.json. Questo comando distribuisce il cluster in un gruppo di risorse creato dall'utente e denominato `myResourceGroup` nell'area Stati Uniti occidentali.

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus"

    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


## <a name="option-2-generate-a-service-principal-when-creating-the-cluster-with-az-acs-create"></a>Opzione 2: generare un'entità servizio durante la creazione del cluster con `az acs create`

Se si esegue il comando [`az acs create`](/cli/azure/acs#create) per creare il cluster Kubernetes, è possibile scegliere di generare automaticamente un'entità servizio.

Analogamente alle altre opzioni di creazione del cluster Kubernetes, è possibile specificare i parametri per un'entità servizio esistente quando si esegue `az acs create`. Quando tuttavia si omettono questi parametri, l'interfaccia della riga di comando di Azure crea automaticamente un'entità servizio da usare con il servizio contenitore. Questa operazione viene eseguita in modo trasparente durante la distribuzione.

Il comando seguente crea un cluster Kubernetes e genera sia le chiavi SSH che le credenziali dell'entità servizio:

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

> [!IMPORTANT]
> Se l'account non ha le autorizzazioni di Azure AD e della sottoscrizione necessarie per creare un'entità servizio, il comando genera un errore simile a `Insufficient privileges to complete the operation.`
>

## <a name="additional-considerations"></a>Ulteriori considerazioni

* Se non si hanno le autorizzazioni per creare un'entità servizio nella sottoscrizione, potrebbe essere necessario chiedere all'amministratore di Azure AD o della sottoscrizione di assegnare le autorizzazioni necessarie oppure chiedere che venga creata un'entità servizio da usare con il servizio contenitore di Azure.

* L'entità servizio per Kubernetes fa parte della configurazione del cluster. Non usare tuttavia l'identità per distribuire il cluster.

* Ogni entità servizio è associata a un'applicazione Azure AD. L'entità servizio per un cluster Kubernetes può essere associata a qualsiasi nome applicazione Azure AD valido, ad esempio `https://www.contoso.org/example`. L'URL per l'applicazione non deve essere necessariamente un endpoint reale.

* Quando si specifica l'**ID client** dell'entità servizio, è possibile usare il valore di `appId`, come illustrato in questo articolo, o il valore `name` corrispondente dell'entità servizio, ad esempio `https://www.contoso.org/example`.

* Nelle macchine virtuali master e dell'agente nel cluster Kubernetes, le credenziali dell'entità servizio vengono archiviate nel file /etc/kubernetes/azure.json.

* Quando si usa il comando `az acs create` per generare automaticamente l'entità servizio, le credenziali dell'entità servizio vengono scritte nel file ~/.azure/acsServicePrincipal.json nel computer usato per eseguire il comando.

* Quando si usa il comando `az acs create` per generare automaticamente l'entità servizio, questa può anche eseguire l'autenticazione con un [registro contenitori di Azure](../../container-registry/container-registry-intro.md) creato nella stessa sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione a Kubernetes](container-service-kubernetes-walkthrough.md) nel cluster del servizio contenitore.

* Per risolvere i problemi dell'entità servizio per Kubernetes, vedere la [documentazione del motore ACS](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes.md#troubleshooting).