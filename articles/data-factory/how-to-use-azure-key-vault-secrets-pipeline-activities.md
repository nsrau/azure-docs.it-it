---
title: Usare i segreti di Azure Key Vault nelle attività della pipeline
description: Informazioni su come recuperare le credenziali archiviate dall'insieme di credenziali delle chiavi di Azure e usarle durante l'esecuzione della pipeline della data factory.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 09051ad3633ddc720cb34d3d145ccf649fa9cb08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77200113"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Usare i segreti di Azure Key Vault nelle attività della pipeline

È possibile archiviare credenziali o valori segreti in un insieme di credenziali delle chiavi di Azure e usarli durante l'esecuzione della pipeline per passare alle attività.

## <a name="prerequisites"></a>Prerequisiti

Questa funzionalità si basa sull'identità gestita dalla data factory.  Informazioni sul funzionamento da [Managed identity for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) e verificare che alla data factory ne sia associata una.

## <a name="steps"></a>Passaggi

1. Aprire le proprietà della data factory e copiare il valore ID applicazione identità gestita.

    ![Valore identità gestita](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Aprire i criteri di accesso dell'insieme di credenziali delle chiavi e aggiungere le autorizzazioni dell'identità gestita a i segreti Get ed List.

    ![Criteri di accesso di Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Criteri di accesso di Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Fare clic su **Aggiungi**, quindi su **Salva**.

3. Passare al segreto dell'insieme di credenziali delle chiavi e copiare l'identificatore segreto.

    ![Identificatore segreto](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Prendere nota dell'URI segreto che si vuole ottenere durante l'esecuzione della pipeline della data factory.

4. Nella pipeline di Data Factory aggiungere una nuova attività Web e configurarla come segue.  

    |Proprietà  |valore  |
    |---------|---------|
    |Uscita sicura     |True         |
    |URL     |[Valore URI segreto]?api-version         |
    |Metodo     |GET         |
    |Authentication     |Identità del servizio gestita         |
    |Risorsa        |https://vault.azure.net       |

    ![Attività Web](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > È necessario aggiungere **?api-version-7.0** alla fine dell'URI segreto.  

    > [!CAUTION]
    > Impostare l'opzione Output sicuro su true per impedire che il valore segreto venga registrato in testo normale.  Tutte le altre attività che utilizzano questo valore devono avere l'opzione Input sicuro impostata su true.

5. Per utilizzare il valore in un'altra attività, utilizzare l'espressione di codice seguente ** @activity('Web1').output.value**.

    ![Espressione di codice](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare l'insieme di credenziali delle chiavi di Azure per archiviare le credenziali per gli archivi dati e i calcoli, vedere [Archiviare le credenziali in Archiviazione chiavi di AzureTo](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) learn how to use Azure Key Vault to store credentials for data stores and computes, see Store credentials in Azure Key Vault
