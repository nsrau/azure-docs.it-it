---
title: Usare le identità gestite di Azure per creare ambienti in DevTest Labs. Documenti Microsoft
description: Informazioni su come usare le identità gestite in Azure per distribuire ambienti in un lab in Azure DevTest Labs.Learn how to use managed identities in Azure to deploy environments in a lab in Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: spelluru
ms.openlocfilehash: a4ba4206c01e492f2ae980c5806de1e72c7051c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931148"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Usare le identità gestite di Azure per distribuire ambienti in un labUse Azure managed identities to deploy environments in a lab 
Il proprietario di un lab può usare un'identità gestita per distribuire ambienti in un lab. Questa funzionalità è utile negli scenari in cui l'ambiente contiene o contiene riferimenti a risorse di Azure, ad esempio insiemi di credenziali delle chiavi, raccolte di immagini condivise e reti esterne al gruppo di risorse dell'ambiente. Consente la creazione di ambienti sandbox che non sono limitati al gruppo di risorse di tale ambiente.

> [!NOTE]
> Attualmente, una singola identità assegnata dall'utente è supportata per laboratorio. 

## <a name="prerequisites"></a>Prerequisiti
- [Creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente tramite il portale](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)di Azure. 

## <a name="use-azure-portal"></a>Usare il portale di Azure
In questa sezione, in qualità di proprietario del lab, usare il portale di Azure per aggiungere un'identità gestita dall'utente al lab. 

1. Nella pagina del lab selezionare **Configurazione e criteri**. 
1. Selezionare **Identità** nella sezione **Impostazioni.Select** Identity in the Settings section.
1. Per aggiungere un'identità assegnata dall'utente, selezionare **Aggiungi** sulla barra degli strumenti. 
1. Selezionare **un'identità** da un elenco a discesa prepopolato.
1. Selezionare **OK**.

    ![Aggiungere l'identità gestita dall'utenteAdd user-managed identity](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. Nell'elenco viene visualizzata l'identità gestita dall'utente aggiunta. 

    ![Identità gestita dall'utente nell'elenco](./media/use-managed-identities-environments/identity-in-list.png)

Una volta salvato, il lab utilizzerà questa identità durante la distribuzione di tutti gli ambienti lab. È anche possibile accedere alla risorsa identità in Azure selezionando l'identità dall'elenco. 

Il proprietario del lab non deve eseguire alcuna operazione speciale durante la distribuzione di un ambiente, purché l'identità aggiunta al lab disponga delle autorizzazioni per le risorse esterne a cui l'ambiente deve accedere. 

Per modificare l'identità gestita dall'utente assegnata al lab, rimuovere prima l'identità associata al lab e quindi aggiungerne un'altra al lab. Per rimuovere un'identità associata al lab, selezionare **... (i lipsiane)** e fare clic su **Rimuovi**. 

![Identità gestita dall'utente nell'elenco](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>Utilizzare l'API

1. Dopo aver creato un'identità, prendere nota dell'ID risorsa di questa identità. Dovrebbe essere simile all'esempio seguente:It should look like the following sample: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Eseguire un metodo PUT Https `ServiceRunner` per aggiungere una nuova risorsa al lab simile all'esempio seguente. La risorsa service runner è una risorsa proxy per gestire e controllare le identità gestite in DevTest Labs. Il nome del corridore del servizio può essere qualsiasi nome valido, ma è consigliabile usare il nome della risorsa di identità gestita. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    Ad esempio: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
Dopo aver aggiunto l'identità assegnata all'utente al lab, il servizio Azure DevTest Labs la userà durante la distribuzione di ambienti Azure Resource Manager.Once the user assigned identity is added to the lab, the Azure DevTest Labs service will use it while deploying Azure Resource Manager environments. Ad esempio, se è necessario il modello di Resource Manager per accedere a un'immagine esterna della raccolta di immagini condivise, assicurarsi che l'identità aggiunta al lab disponga delle autorizzazioni minime necessarie per la risorsa raccolta immagini condivise. 
