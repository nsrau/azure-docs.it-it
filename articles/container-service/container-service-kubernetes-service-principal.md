---
title: "Entità servizio per cluster Azure Kubernetes | Documentazione Microsoft"
description: "Creare e gestire un&quot;entità servizio di Azure Active Directory in un cluster del servizio contenitore di Azure con Kubernetes"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 5af9b5fdaf228edd54900855d0eac5d90ea3db38
ms.openlocfilehash: 0121896aa27677080d6b240fdafff3c7e19683d9


---

# <a name="about-the-azure-active-directory-service-principal-for-a-kubernetes-cluster-in-azure-container-service"></a>Informazioni sull'entità servizio di Azure Active Directory per un cluster Kubernetes nel servizio contenitore di Azure



Kubernetes richiede un'[entità servizio di Azure Active Directory](../active-directory/active-directory-application-objects.md) nel servizio contenitore di Azure come account del servizio per l'interazione con le API di Azure. L'entità servizio è necessaria per la gestione dinamica di risorse quali le route definite dall'utente e Azure Load Balancer di livello 4.

Questo articolo illustra le diverse opzioni disponibili per specificare un'entità servizio per il cluster Kubernetes. Se, ad esempio, l'[interfaccia della riga di comando di Azure 2.0 (anteprima)](https://docs.microsoft.com/cli/azure/install-az-cli2) è già stata installata e configurata, è possibile eseguire il comando [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) per creare il cluster Kubernetes e l'entità servizio contemporaneamente.

> [!NOTE]
> Il supporto per Kubernetes nel servizio contenitore di Azure è attualmente disponibile in anteprima.


## <a name="requirements-for-the-service-principal"></a>Requisiti per l'entità servizio

Di seguito sono disponibili i requisiti per l'entità servizio di Azure Active Directory in un cluster Kubernetes nel servizio contenitore di Azure. 

* **Ambito**: la sottoscrizione di Azure in cui è distribuito il cluster.

* **Ruolo**: **Collaboratore**.

* **Segreto client**: deve essere una password. Non è attualmente possibile usare un'entità servizio configurata per l'autenticazione del certificato.

> [!NOTE]
> Ogni entità servizio è associata a un'applicazione Azure Active Directory. L'entità servizio per un cluster Kubernetes può essere associata a qualsiasi nome applicazione Azure Active Directory valido.
> 


## <a name="service-principal-options-for-a-kubernetes-cluster"></a>Opzioni dell'entità servizio per un cluster Kubernetes

### <a name="option-1-pass-the-service-principal-client-id-and-client-secret"></a>Opzione 1: passare l'ID client e il segreto client dell'entità servizio

Specificare l'**ID client**, spesso definito `appId` ovvero ID applicazione, e il **segreto client** (`password`) di un'entità servizio esistente come parametri durante la creazione del cluster Kubernetes. Se si usa un'entità servizio esistente, assicurarsi che rispetti i requisiti specificati nella sezione precedente. Se è necessario creare un'entità servizio, vedere [Creare un'entità servizio](#create-a-service-principal-in-azure-active-directory) più avanti in questo articolo.

È possibile specificare questi parametri durante la [distribuzione del cluster Kubernetes](./container-service-deployment.md) usando il portale, la versione 2.0 dell'interfaccia della riga di comando di Azure (anteprima), Azure PowerShell o altri metodi.

>[!TIP] 
>Quando si specifica l'**ID client**, assicurarsi di usare il valore `appId`, non `ObjectId`, dell'entità servizio.
>

L'esempio seguente illustra un modo per passare i parametri con la versione 2.0 dell'interfaccia della riga di comando di Azure (anteprima). Vedere le [istruzioni di installazione e configurazione](/cli/azure/install-az-cli2). Questo esempio usa il [modello di avvio rapido di Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

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


### <a name="option-2-generate-the-service-principal-when-creating-the-cluster-with-the-azure-cli-20-preview"></a>Opzione 2: generare l'entità servizio durante la creazione del cluster con la versione 2.0 dell'interfaccia della riga di comando di Azure (anteprima)

Se la [versione 2.0 dell'interfaccia della riga di comando di Azure (anteprima)](https://docs.microsoft.com/cli/azure/install-az-cli2) è già stata installata e configurata, è possibile eseguire il comando [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) per [creare il cluster](./container-service-create-acs-cluster-cli.md).

Analogamente alle altre opzioni di creazione del cluster Kubernetes, è possibile specificare i parametri per un'entità servizio esistente quando si esegue `az acs create`. Quando tuttavia si omettono questi parametri, il servizio contenitore di Azure crea automaticamente un'entità servizio. Questa operazione viene eseguita in modo trasparente durante la distribuzione. 

Il comando seguente crea un cluster Kubernetes e genera sia le chiavi SSH che le credenziali dell'entità servizio:

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

## <a name="create-a-service-principal-in-azure-active-directory"></a>Creare un'entità servizio in Azure Active Directory

Se si vuole creare un'entità servizio in Azure Active Directory per l'uso nel cluster Kubernetes, in Azure sono disponibili diversi metodi. 

I comandi di esempio seguenti illustrano come eseguire questa operazione con l'[interfaccia della riga di comando Azure 2.0 (anteprima)](https://docs.microsoft.com/cli/azure/install-az-cli2). In alternativa, è possibile creare un'entità servizio usando [Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md), il [portale classico](../azure-resource-manager/resource-group-create-service-principal-portal.md) o altri metodi.

> [!IMPORTANT]
> Assicurarsi di esaminare i requisiti per l'entità servizio illustrati in precedenza in questo articolo.
>

```azurecli
az login

az account set --subscription "mySubscriptionID"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

Viene restituito un output simile al seguente (visualizzato con alcune modifiche):

![Creare un’entità servizio](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

Sono evidenziati l'**ID client** (`appId`) e il **segreto client** (`password`) usati come parametri dell'entità servizio per la distribuzione del cluster.


Confermare l'entità servizio aprendo una nuova shell ed eseguire i comandi seguenti, sostituendo i valori per `appId`, `password` e `tenant`:

```azurecli 
az login --service-principal -u yourClientID -p yourClientSecret --tenant yourTenant

az vm list-sizes --location westus
```

## <a name="additional-considerations"></a>Considerazione aggiuntive


* Quando si specifica l'**ID client** dell'entità servizio, è possibile usare il valore di `appId`, come illustrato in questo articolo, o il valore `name` corrispondente dell'entità servizio, ad esempio `https://www.contoso.org/example`.

* Se si usa il comando `az acs create` per generare automaticamente l'entità servizio, le credenziali dell'entità servizio vengono scritte nel file ~/.azure/acsServicePrincipal.json nel computer usato per eseguire il comando.

* Nelle macchine virtuali master e del nodo nel cluster Kubernetes le credenziali dell'entità servizio vengono archiviate nel file /etc/kubernetes/azure.json.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione a Kubernetes](container-service-kubernetes-walkthrough.md) nel cluster del servizio contenitore.



<!--HONumber=Feb17_HO1-->


