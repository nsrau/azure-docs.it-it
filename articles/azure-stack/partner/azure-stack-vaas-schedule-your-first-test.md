---
title: Usare la convalida come servizio per il portale di Azure Stack per pianificare il primo test | Microsoft Docs
description: Utilizzare la convalida come servizio per il portale di Azure Stack per pianificare il primo test.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 0f681070df4b4b3384171c05edb3851abec2ab5c
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235409"
---
# <a name="quickstart-use-the-validation-as-a-service-portal-to-schedule-your-first-test"></a>Guida introduttiva: Usare la convalida come portale del servizio per pianificare il primo test

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Informazioni su come pianificare il primo test con la convalida di un portale del servizio (VaaS) per controllare l'hardware. La soluzione di Azure Stack in fase di convalida prima di eseguire il test di convalida occorre installarvi l'agente locale.

In questa Guida introduttiva verrà aggiunta la soluzione ed eseguire i test.

## <a name="prerequisites"></a>Prerequisiti

Prima di seguire questa Guida introduttiva, è necessario avere:
 - Una convalida come account del servizio. Per istruzioni, vedere [configurare la convalida come account del servizio](azure-stack-vaas-set-up-account.md).  
- L'agente locale installata nel sistema. Per istruzioni, vedere [distribuire le macchine virtuali locali di test e agenti](azure-stack-vaas-test-vm.md).

## <a name="add-a-new-solution"></a>Aggiungere una nuova soluzione

1. Accedi per il [portale convalida](https://azurestackvalidation.com).

    ![Accedere al portale di convalida](media/vaas_portalsignin.png)  

2. Selezionare **nuova soluzione**.
3. Immettere un nome per la soluzione e selezionare **salvare**.

## <a name="create-a-solution-validation-workflow"></a>Creare un flusso di lavoro di convalida di soluzioni

1. Selezionare il nome della soluzione.
2. Selezionare **Manage** nel **soluzione convalide** riquadro.

    ![Convalide di soluzione](media/image2.png)

## <a name="create-a-solution-workflow"></a>Creare un flusso di lavoro di soluzione

1. Selezionare **nuova convalida soluzione**.
2. Digitare il nome della convalida.
3. Selezionare **minimo** oppure **massimo**.  
    - **Minimi**  
    La soluzione è configurata con il numero di nodi minimo supportato.  
    - **Valore massimo**  
    La soluzione è configurata con il numero massimo supportato di nodi.
4. Aggiungere i parametri dell'ambiente. Per altre informazioni, vedere [aggiungere i parametri ambientali](#add-environmental-parameters).
5. Aggiungere i parametri comuni di test. Per altre informazioni, vedere [aggiungere i parametri di test comuni](#add-common-test-parameters).

    A seconda della definizione di test, il test potrebbe essere necessario immettere un valore indipendentemente dai parametri comuni, o può consentire è possibile sostituire il valore del parametro comune.
6. Fare clic su **Submit** per il test.

## <a name="add-environmental-parameters"></a>Aggiungere parametri ambientali

Aggiungere i parametri di ambienti seguenti:

| Informazioni sul passaggio di test | Obbligatorio | Description |
| --- | --- | --- | --- |
| Compilazione di Azure Stack | Obbligatorio | Valore numerico (ad esempio 20170501.1) deve essere una valida di Azure Stack compilare numero o la versione, ad esempio, 1.0.170330.9 Azure build di Stack |
| ID tenant | Obbligatorio | ID Tenant di Active Directory. Deve essere un GUID (ad esempio ECA23256-6BA0-4F27-8E4D-AFB02F088363) |
| Region | Obbligatorio | Area di distribuzione di Azure Stack |
| Endpoint di Resource Manager tenant | Obbligatorio | Endpoint per le operazioni di Tenant di Azure Resource Manager (ad esempio https://management.loc-ext.domain.com) |
| Endpoint amministratore Resource Manager | Facoltativo | Endpoint per le operazioni di Tenant di Azure Resource Manager (ad esempio https://management.loc-ext.domain.com) |
| Nome di dominio completo esterna | Facoltativo | Nome di dominio usato come suffisso per gli endpoint esterno completo. (ad esempio local.azurestack.external o redmond.contoso.com) |
| Numero di nodi | Obbligatorio | Il numero di nodi nella soluzione. |

## <a name="add-common-test-parameters"></a>Aggiungere parametri comuni di test

Aggiungere i parametri di test comuni seguenti:

| Informazioni sul passaggio di test | Obbligatorio | Description |
| --- | --- | --- |
| Nome utente tenant | Obbligatorio | Nome utente del tenant (ad esempio tenant@contoso.onmicrosoft.com) |
| Password del tenant | Obbligatorio | La password per il tenant. |
| Nome utente amministratore del servizio | Facoltativo | Nome utente del tenant (ad esempio tenant@contoso.onmicrosoft.com) |
| Password amministratore del servizio | Facoltativo | Nome utente amministratore del servizio (ad esempio serviceadmin@contoso.onmicrosoft.com) |
| Nome utente amministratore del cloud | Facoltativo | Account di amministratore di Azure Stack dominio (ad esempio, contoso\cloudadmin) |
| Password amministratore del cloud | Facoltativo | |
|  Stringa di connessione di diagnostica | Facoltativo | URI SAS a un Account di archiviazione di Azure per la diagnostica quali log verranno copiati durante l'esecuzione di test. Visualizzare [creare un blob di archiviazione di Azure per archiviare i log](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs). <br><br>Il valore della **stringa di connessione di diagnostica** parametro comune di verrà archiviata dal servizio e fornito in fase di pianificazione per tutti i test nel flusso di lavoro che utilizza questo parametro. Quando l'URL di firma di accesso condiviso è entro 30 giorni dalla scadenza, verrà richiesto per un nuovo URL di firma di accesso condiviso nella pagina parametri comuni. |
| Tag - Name | Facoltativo |  Tag descrittivi possono essere immessi per etichettare il flusso di lavoro. Si tratta del nome del tag. |
| Tag - valore | Facoltativo | Tag descrittivi possono essere immessi per etichettare il flusso di lavoro. Questo è il valore del tag. |

## <a name="next-steps"></a>Passaggi successivi

- [Convalidare una nuova soluzione di Azure Stack](azure-stack-vaas-validate-solution-new.md)  
- Per altre informazioni sulle [convalida Azure Stack come servizio](https://docs.microsoft.com/azure/azure-stack/partner).
