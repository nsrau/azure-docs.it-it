---
title: Usare le identità gestite di Azure per creare ambienti in DevTest Labs | Microsoft Docs
description: Informazioni su come usare le identità gestite in Azure per distribuire gli ambienti in un Lab in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e5bac4210afee6db1c7617dac1cd6d2ff9149439
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88718978"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Usare le identità gestite di Azure per distribuire gli ambienti in un Lab 

In qualità di proprietario del Lab, è possibile usare un'identità gestita per distribuire gli ambienti in un Lab. Questa funzionalità è utile negli scenari in cui l'ambiente contiene o contiene riferimenti alle risorse di Azure, ad esempio insiemi di credenziali delle chiavi, raccolte di immagini condivise e reti esterne al gruppo di risorse dell'ambiente. Consente la creazione di ambienti sandbox che non sono limitati al gruppo di risorse dell'ambiente.

> [!NOTE]
> Attualmente, per Lab è supportata una singola identità assegnata dall'utente. 

## <a name="prerequisites"></a>Prerequisiti

- [Creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente usando il portale di Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 
    
    Assicurarsi che l'identità gestita sia stata creata nella stessa area e nella stessa sottoscrizione del Lab. Non è necessario che l'identità gestita si trovi nello stesso gruppo di risorse.

## <a name="use-azure-portal"></a>Usare il portale di Azure

In questa sezione, in qualità di proprietario del Lab, usare il portale di Azure per aggiungere un'identità gestita dall'utente al Lab. 

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare **DevTest Labs**.
1. Dall'elenco dei Lab selezionare il Lab desiderato.
1. Selezionare **configurazione e criteri**  ->  **identità (anteprima)**. 
1. Per aggiungere un'identità assegnata a un utente, selezionare la scheda **assegnato dall'utente** .
1. Premere **Aggiungi** .
1. Selezionare un utente esistente che è stato creato e/o a cui si ha accesso dall'elenco a discesa.
 
    ![Aggiungi identità gestita dall'utente](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. Fare clic su **Salva** nella parte superiore della pagina.

    Una volta salvato, il Lab utilizzerà questa identità durante la distribuzione di tutti gli ambienti Lab. È anche possibile accedere alla risorsa di identità in Azure selezionando l'identità dall'elenco. 

Il proprietario del Lab non deve eseguire alcuna operazione speciale durante la distribuzione di un ambiente purché l'identità aggiunta al lab disponga delle autorizzazioni per le risorse esterne a cui l'ambiente deve accedere. 

Per modificare l'identità gestita dall'utente assegnata al Lab, rimuovere prima l'identità collegata al Lab, quindi aggiungerne un'altra al Lab. Per rimuovere un'identità collegata al Lab, selezionare **... (puntini**di sospensione) e fare clic su **Rimuovi**. 

## <a name="use-api"></a>USA API

1. Dopo aver creato un'identità, prendere nota dell'ID risorsa dell'identità. Dovrebbe essere simile all'esempio seguente: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Eseguire un metodo PUT HTTPS per aggiungere una nuova `ServiceRunner` risorsa al Lab in modo analogo all'esempio seguente. La risorsa di Service Runner è una risorsa proxy per gestire e controllare le identità gestite in DevTest Labs. Il nome del Runner del servizio può essere qualsiasi nome valido, ma è consigliabile usare il nome della risorsa di identità gestita. 
 
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
 
    Ecco un esempio: 

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
 
Una volta aggiunta l'identità assegnata all'utente al Lab, il servizio Azure DevTest Labs lo utilizzerà durante la distribuzione di Azure Resource Manager ambienti. Se ad esempio è necessario il modello di Gestione risorse per accedere a un'immagine della raccolta di immagini condivise esterna, assicurarsi che l'identità aggiunta al lab disponga delle autorizzazioni minime necessarie per la risorsa della raccolta immagini condivisa. 
