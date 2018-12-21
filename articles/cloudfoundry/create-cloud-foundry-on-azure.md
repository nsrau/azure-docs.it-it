---
title: Creare un cluster Pivotal Cloud Foundry in Azure
description: Informazioni su come configurare i parametri necessari per eseguire il provisioning di un cluster Pivotal Cloud Foundry (PCF) in Azure
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: Cloud Foundry
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: 9514118e1f29faab937ed01899b5947789ca9735
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101399"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Creare un cluster Pivotal Cloud Foundry in Azure

Questa esercitazione fornisce i passaggi rapidi per creare e generare i parametri necessari per eseguire il provisioning di un cluster Pivotal Cloud Foundry (PCF) in Azure. Per trovare la soluzione Pivotal Cloud Foundry, eseguire una ricerca in Azure [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Ricercare Pivotal Cloud Foundry in Azure](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Generare una chiave pubblica SSH

Esistono diversi modi per generare una chiave SSH pubblica con Windows, Mac o Linux.

```Bash
ssh-keygen -t rsa -b 2048
```

Per altre informazioni, vedere [Uso delle chiavi SSH con Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

## <a name="create-a-service-principal"></a>Creare un'entità servizio

> [!NOTE]
>
> Per creare un'entità servizio è necessaria l'autorizzazione di un account proprietario. Inoltre, è possibile scrivere uno script per automatizzare la creazione dell'entità servizio. Ad esempio, usando l'interfaccia della riga di comando di Azure [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest).

1. Accedere all'account Azure.

    `az login`

    ![Login dell'interfaccia della riga di comando di Azure](media/deploy/az-login-output.png )
 
    Copiare il valore "id" come l'**ID sottoscrizione** e il valore "tenantId" da usare in seguito.

2. Impostare la sottoscrizione predefinita per questa configurazione.

    `az account set -s {id}`

3. Creare un'applicazione di Azure Active Directory per il PCF. Specificare una password alfanumerica univoca. Archiviare la password come **clientSecret** da usare successivamente.

    `az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Copiare il valore "appId" nell'output come **clientID** da usare successivamente.

    > [!NOTE]
    >
    > Scegliere l'home page dell'applicazione e l'URI dell'identificatore, ad esempio http://www.contoso.com.

4. Creare un'entità servizio con il nuovo ID app.

    `az ad sp create --id {appId}`

5. Impostare il ruolo di autorizzazione dell'entità servizio come Collaboratore.

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor” `

    In alternativa, è anche possibile usare

    `az role assignment create --assignee {service-princ-name} --role “Contributor” `

    ![Assegnazione di ruolo dell'entità servizio](media/deploy/svc-princ.png )

6. Verificare di poter accedere correttamente all'entità servizio usando l'ID app, la password e l'ID tenant.

    `az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}`

7. Creare un file .json con il formato seguente. Usare i valori **ID sottoscrizione**, **tenantID**, **clientID** e **clientSecret** copiati in precedenza. Salvare il file.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Ottenere il valore Pivotal Network Token

1. Registrarsi o accedere all'account di [Pivotal Network](https://network.pivotal.io).
2. Selezionare il nome profilo nell'angolo superiore destro della pagina. Selezionare **Modifica profilo**.
3. Scorrere fino alla fine della pagina e copiare il valore **LEGACY API TOKEN**. Questo è il valore **Pivotal Network Token** da usare successivamente.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Eseguire il provisioning del cluster Cloud Foundry in Azure

A questo punto si hanno tutti i parametri necessari per eseguire il provisioning del [cluster Pivotal Cloud Foundry in Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
Immettere i parametri e creare il cluster PCF.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>Verificare la distribuzione e accedere a Pivotal Ops Manager

1. Il cluster PCF mostra lo stato della distribuzione.

    ![Stato della distribuzione di Azure](media/deploy/deployment.png )

2. Selezionare il collegamento **Distribuzioni** nel riquadro di navigazione a sinistra per ottenere le credenziali per PCF Ops Manager. Selezionare il **nome della distribuzione** nella pagina successiva.
3. Nel riquadro di navigazione a sinistra, selezionare il collegamento **Output** per visualizzare l'URL, il nome utente e la password per PCF Ops Manager. Il valore "OPSMAN-FQDN" è l'URL.
 
    ![Output della distribuzione di Cloud Foundry](media/deploy/deploy-outputs.png )
 
4. Aprire l'URL in un browser Web. Per accedere, immettere le credenziali del passaggio precedente.

    ![Pagina di accesso di Pivotal](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Se il browser di Internet Explorer restituisce un errore a causa del messaggio di avviso "Sito non sicuro", selezionare **Maggiori informazioni** e passare alla pagina Web. Per procedere in Firefox, selezionare **Avanzate** e aggiungere il certificato.

5. PCF Ops Manager visualizzerà le istanze di Azure distribuite. Ora è possibile distribuire e gestire le applicazioni da qui.
               
    ![Istanza di Azure distribuita in Pivotal](media/deploy/ops-mgr.png )
 
