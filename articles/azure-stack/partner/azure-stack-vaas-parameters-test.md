---
title: Testare i parametri per la convalida come un servizio di Azure Stack | Microsoft Docs
description: Argomento di riferimento sul file di configurazione e test passare i parametri per la convalida come un servizio di Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 65cae4d10a4683207474008a18bac39751ce8e96
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235356"
---
# <a name="test-parameters-for-validation-as-a-service-azure-stack"></a>Parametri di test per la convalida come un servizio di Azure Stack

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Prima di eseguire qualsiasi gruppo di Test da convalide come servizio (VaaS), selezionare una soluzione e creare un test.

- L'accesso con il VaaS registrati credenziali del tenant.
- Creare una nuova soluzione (selezionando **Aggiungi soluzione**) oppure selezionare tutte le classi esistenti.
- Selezionare il **avviare** pulsante il **Test superati** riquadro del flusso di lavoro.

> [!Note]  
> Creare una nuova voce di soluzione per ogni configurazione di set/hardware univoco per la macchina che si sta eseguendo la convalida e passare un nuovo test per ogni distribuzione di compilazione in tale set di computer.

Sarà necessario immettere i parametri necessari per eseguire i test nel **Test passare informazioni** pagina. Specificare questi parametri quando si avvia un nuovo passaggio di test o l'esecuzione di convalida. La maggior parte dei parametri hanno gli stessi valori specificati durante la distribuzione Azure Stack.

È possibile immettere i valori elencati manualmente nella [tabella di parametri di Test](#test-parameters), oppure caricare il file di configurazione di distribuzione, che contiene le informazioni di stamp di Azure Stack complete. Una volta caricato, il portale carica i valori dal file.

> [!Note]  
> È possibile cercare e digitando i valori dei parametri di test da reperire e caricare il file di configurazione nel portale.

## <a name="export-the-test-parameters-using-powershell"></a>Esporta i parametri di test usando PowerShell

1. Accedere al computer DVM o qualsiasi macchina con accesso all'ambiente Azure Stack.
2. Aprire una finestra di PowerShell con privilegi elevata ed eseguire:

    ````PowerShell  
        $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation'
        ConvertTo-Json $params > stampinfoproperties.json
    ````

3. Caricare **stampinfoproperties.json** al portale VaaS.

## <a name="find-the-test-parameters-in-the-configuration-file"></a>Trovare i parametri di test nel file di configurazione

È anche possibile trovare il test di valori dei parametri aprendo il ECE **file di configurazione**. È possibile trovare il file nel percorso seguente del computer di DVM:  
`C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787`

## <a name="test-parameters"></a>Parametri di test 

| Parametro    | Description |
|-------------|-----------------|
| Compilazione di Azure Stack                      | Numero di Stack di compilazione o versione Azure che è stato distribuito, ad esempio, 1.0.170330.0 | 
| ID tenant                              | Azure Active Directory Tenant specificato durante la distribuzione di Azure Stack. Cercare **AADTenant** nel file di configurazione e selezionare il **GUID** valore il `Id` tag. | 
| Region                                 | Area di distribuzione di Azure Stack. Cercare **regione** nel file di configurazione. | 
| Tenant di Resource Manager                | Tenant endpoint Azure Resource Manager, ad esempio, `https://management.<ExternalFqdn>` | 
| Amministrazione di Resource Manager                 | Endpoint amministratore Azure Resource Manager. ad esempio `https://adminmanagement.<ExternalFqdn>` | 
| Nome di dominio completo esterna                          | Nome completo esterna dell'ambiente. Cercare **ExternalFqdn** nel file di configurazione. | 
| Utente del tenant                            | Amministratore del Tenant di Active Directory che sia stato effettuato il provisioning già o dovrà essere sottoposte a provisioning l'amministratore del servizio nella Directory di Azure AD Azure. Per informazioni dettagliate sul provisioning degli account del tenant, vedere [aggiungere un nuovo account tenant di Azure Stack in Azure Active Directory](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad). Questo valore viene utilizzato dal test per eseguire operazioni a livello di tenant, ad esempio la distribuzione di modelli per eseguire il provisioning delle risorse (account della macchina virtuale, archiviazione e così via) ed eseguire i carichi di lavoro. | 
| Utente amministratore del servizio             | Azure amministratore di Active Directory del Tenant di Azure Active Directory specificato durante la distribuzione di Azure Stack. Cercare **AADTenant** nella configurazione di file e selezionare il valore nel `UniqueName` tag nel file di configurazione. | 

## <a name="checks-before-starting-the-tests"></a>Controlla prima di avviare i test

I test di eseguono operazioni remote. Il computer che esegue il test deve avere accesso agli endpoint di Azure Stack. In caso contrario, il test non funzionerà. Se si usa il VaaS nell'agente locale, usare la macchina in cui verrà eseguito l'agente. È possibile verificare che il computer abbia accesso ai punti di Azure Stack eseguendo i controlli seguenti.

1. Controllare che l'URI di Base può essere raggiunto. Aprire un prompt dei comandi o shell bash ed eseguire il comando seguente:

    ```bash  
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Aprire un web browser e passare a `https://adminportal.<EXTERNALFQDN>` per controllare che il portale di agenti di gestione può essere raggiunto.

3. Accedere con Azure AD del servizio i valori di nome e la password amministratore specificati durante la creazione il superamento del test.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle [convalida Azure Stack come servizio](https://docs.microsoft.com/azure/azure-stack/partner).
