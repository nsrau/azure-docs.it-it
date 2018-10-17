---
title: Creare Cloud Foundry in Azure
description: Informazioni su come impostare i parametri necessari per eseguire il provisioning di un cluster Cloud Foundry PCF in Azure
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
ms.openlocfilehash: a0a3379a8a2579080d9b686917395feec9cf8f3d
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48250626"
---
# <a name="create-cloud-foundry-on-azure"></a>Creare Cloud Foundry in Azure

Questa esercitazione fornisce i passaggi rapidi per creare e generare i parametri necessari per eseguire il provisioning di un cluster Pivotal Cloud Foundry PCF in Azure.  Per trovare la soluzione Pivotal Cloud Foundry, eseguire una ricerca in Azure [MarketPlace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Testo immagine alt](media/deploy/pcf-marketplace.png "Cercare Pivotal Cloud Foundry in Azure")


## <a name="generate-an-ssh-public-key"></a>Generare una chiave pubblica SSH

Esistono diversi modi per generare una chiave pubblica SSH con Windows, Mac o Linux.

```Bash
ssh-keygen -t rsa -b 2048
```
- Fare clic qui per vedere le [istruzioni]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) per l'ambiente usato.

## <a name="create-a-service-principal"></a>Creare un'entità servizio

> [!NOTE]
>
> Per creare un'entità servizio è necessaria l'autorizzazione di un account proprietario.  Inoltre, è possibile scrivere uno script per automatizzare la creazione dell'entità servizio. Ad esempio, usando l'interfaccia della riga di comando di Azure [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest).

1. Accedere all'account Azure.

    `az login`

    ![Testo immagine alt](media/deploy/az-login-output.png "Accesso all'interfaccia della riga di comando di Azure")
 
    Copiare il valore "id" come l'**ID sottoscrizione** e il valore **tenantId** da usare più avanti.

2. Impostare la sottoscrizione predefinita per questa configurazione.

    `az account set -s {id}`

3. Creare un'applicazione AAD per PCF e specificare una password alfanumerica univoca.  Archiviare la password come **clientSecret** da usare più avanti.

    `az ad app create --display-name "Svc Prinicipal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Copiare il valore "appId" nell'output come **ClientID** da usare più avanti.

    > [!NOTE]
    >
    > Scegliere la propria home page dell'applicazione e l'URI dell'identificatore.  ad esempio http://www.contoso.com.

4. Creare un'entità servizio con il nuovo valore "appId".

    `az ad sp create --id {appId}`

5. Impostare il ruolo dell'autorizzazione dell'entità servizio come **Collaboratore**.

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor” `

    In alternativa, è anche possibile usare…

    `az role assignment create --assignee {service-princ-name} --role “Contributor” `

    ![Testo immagine alt](media/deploy/svc-princ.png "assegnazione del ruolo dell'entità servizio")

6. Verificare di poter accedere correttamente all'entità servizio usando i valori appId, password e tenantId.

    `az login --service-principal -u {appId} -p {your-passward}  --tenant {tenantId}`

7. Creare un file con estensione JSON nel formato seguente usando tutti i valori precedenti copiati: **ID sottoscrizione**, **tenantId**, **clientID** e **clientSecret**.  Salvare il file.

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
2. Fare clic sul nome del profilo nella pagina in alto a destra, quindi selezionare "Edit Profile" (Modifica profilo).
3. Scorrere fino alla fine della pagina e copiare il valore **LEGACY API TOKEN**.  Questo è il valore **Pivotal Network Token** che verrà usato più avanti.

## <a name="provision-your-cloud-foundry-on-azure"></a>Eseguire il provisioning di Cloud Foundry in Azure

1. A questo punto si hanno tutti i parametri necessari per eseguire il provisioning del cluster [Pivotal Cloud Foundry in Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
2. Immettere i parametri e creare il cluster PFC.

## <a name="verify-the-deployment-and-log-into-the-pivotal-ops-manager"></a>Verificare la distribuzione e accedere a Pivotal Ops Manager

1. Il cluster PFC deve mostrare lo stato della distribuzione.

    ![Testo immagine alt](media/deploy/deployment.png "Stato della distribuzione di Azure")

2. Fare clic sul collegamento **Deployments** (Distribuzioni) nel riquadro di spostamento a sinistra per ottenere le credenziali per PCF Ops Manager, quindi fare clic sul collegamento **Deployment Name** (Nome distribuzione) nella pagina successiva.
3. Nel riquadro di spostamento a sinistra fare clic sul collegamento **Outputs** (Output) per visualizzare l'URL, il nome utente e la password per PCF Ops Manager.  Il valore "OPSMAN-FQDN" è l'URL.
 
    ![Testo immagine alt](media/deploy/deploy-outputs.png "Output della distribuzione di Cloud Foundry")
 
4. Avviare l'URL in un Web browser e immettere le credenziali del passaggio precedente per l'accesso.

    ![Testo immagine alt](media/deploy/pivotal-login.png "Pagina di accesso di Pivotal")
         
    > [!NOTE]
    >
    > Se nel browser Internet Explorer si verifica un errore e viene restituito il messaggio di avviso sito non sicuro, fare clic su "Altre informazioni" e passare alla pagina Web.  Per Firefox fare clic su Avanzate e aggiungere la certificazione per continuare.

5. PCF Ops Manager visualizzerà le istanze di Azure distribuite. Ora è possibile avviare la distribuzione e la gestione delle applicazioni.
               
    ![Testo immagine alt](media/deploy/ops-mgr.png "Istanza di Azure distribuita in Pivotal")
 
