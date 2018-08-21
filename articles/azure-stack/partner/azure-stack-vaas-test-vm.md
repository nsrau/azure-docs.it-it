---
title: Distribuire l'agente locale e macchine virtuali di immagini di test nella convalida di Azure Stack come servizio | Microsoft Docs
description: Distribuire l'agente locale e immagini di macchine virtuali per la convalida di Azure Stack come servizio di test.
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
ms.openlocfilehash: 9d32c23f66563988d023df3bf6a33efa30237e57
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235389"
---
# <a name="deploy-the-local-agent-and-test-virtual-machines"></a>Distribuire le macchine virtuali locali di test e agenti

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Informazioni su come usare la convalida come un agente locale del servizio (VaaS) per controllare l'hardware. La soluzione di Azure Stack in fase di convalida prima di eseguire il test di convalida occorre installarvi l'agente locale.

> [!Note]  
> È necessario assicurarsi che la macchina, in cui è in esecuzione l'agente locale, non perdita l'accesso a Internet. La macchina deve solo essere accessibile agli utenti autorizzati a usare Azure Stack VaaS.

Per le macchine virtuali di test:

1. Installare l'agente locale
2. Inserire gli errori nel sistema
3. Eseguire l'agente locale

## <a name="download-and-start-the-local-agent"></a>Scaricare e avviare l'agente locale

Scaricare l'agente in un computer che soddisfi i prerequisiti nel tuo Data Center che non fa parte del sistema Azure Stack, ma quello che ha accesso a tutti gli endpoint di Azure Stack.

### <a name="machine-prerequisites"></a>Prerequisiti dei computer

Verificare che il computer soddisfi i criteri seguenti:

- Accesso a tutti gli endpoint di Azure Stack
- .NET 4.6 e PowerShell 5.0 installato
- Almeno 8 GB di RAM
- Processori di almeno 8 core
- Spazio su disco minimo di 200 GB
- Connettività di rete a internet stabile

Azure Stack è il sistema sottoposto a test. Il computer non deve far parte di Azure Stack o ospitati nel cloud Azure Stack.

### <a name="download-and-install-the-agent"></a>Scaricare e installare l'agente

1. Aprire Windows PowerShell in un prompt dei comandi con privilegi elevati nel computer che userà per eseguire i test.
2. Eseguire il comando seguente per scaricare l'agente locale:

    ```PowerShell  
        Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.3.2.0.nupkg" -outfile "OnPremAgent.zip"
        Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent.3.2.0 -Force
        Set-Location VaaSOnPremAgent.3.2.0\lib\net46
    ````

3. Eseguire il comando seguente per installare le dipendenze dell'agente locale:

    ```PowerShell  
        $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-VaaSPrerequisites -AadTenantId <AadTenantId> `
        -ServiceAdminCreds <ServiceAdminCreds> `
        -ArmEndpoint https://adminmanagement.<ExternalFqdn> `
        -Region <Region>
    ````

    **Parameters**

    | Parametro | Description |
    | --- | --- |
    | aadServiceAdminUser | L'utente amministratore globale per il tenant di Azure AD. Ad esempio potrebbe essere, vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | La password per l'utente amministratore globale. |
    | AadTenantId | ID tenant di Azure AD per l'account di Azure registrati con la convalida come servizio. |
    | ExternalFqdn | È possibile ottenere il nome di dominio completo del file di configurazione. Per istruzioni, vedere [testare i parametri per la convalida come un servizio di Azure Stack](azure-stack-vaas-parameters-test.md). |
    | Region | L'area del tenant di Azure AD. |

Il comando Scarica un'immagine del repository (repository) immagine pubblica (file VHD del sistema operativo) e copiare da un archivio blob di Azure nella risorsa di archiviazione di Azure Stack. 

![Scarica prerequisiti](media/installingprereqs.png)

> [!Note]  
> Se si verificano velocità di rete lenta durante il download di queste immagini, eseguirne il download separatamente in una condivisione locale e specificare il parametro **- LocalPackagePath** *FileShareOrLocalPath*. È possibile trovare ulteriori indicazioni durante il download del repository nella sezione [gestiscono la connettività di rete lenta](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) dei [risolvere i problemi di convalida come servizio](azure-stack-vaas-troubleshoot.md).

## <a name="fault-injection"></a>Approccio di fault injection

Microsoft ha progettato Azure Stack per la resilienza e tolleranza più tipi di errori hardware e software. Approccio di fault injection aumenta la frequenza di errori nel sistema. Questo aumento consente di individuare i problemi in precedenza, in modo che sia possibile ridurre il numero di eventi imprevisti ad arrestare il sistema.

Eseguire i comandi seguenti per inserire gli errori nel sistema.

1. Aprire Windows PowerShell in un prompt dei comandi con privilegi elevati.

2. Eseguire il comando seguente:

    ```PowerShell  
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-ServiceFabricSDK Install-ServiceFabricSDK
    ```

## <a name="run-the-agent"></a>Eseguire l'agente

1. Aprire Windows PowerShell in un prompt dei comandi con privilegi elevati.

2. Eseguire il comando seguente:

    ````PowerShell  
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ````

      **Parameters**  
    
    | Parametro | Description |
    | --- | --- |
    | VaaSUserId | ID utente usato per accedere al portale VaaS (ad esempio, UserName@Contoso.com) |
    | VaaSTenantId | ID tenant di Azure AD per l'account di Azure registrati con la convalida come servizio. |

    > [!Note]  
    > Quando si esegue l'agente, la directory di lavoro corrente deve essere il percorso del file eseguibile, l'host del motore attività **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Se non viene visualizzato di eventuali errori rilevati, l'agente locale è stata completata. Il testo di esempio seguente viene visualizzato nella finestra della console.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Agente avviato](media/startedagent.png)

Un agente viene identificato in base al relativo nome. Per impostazione predefinita, Usa il nome di dominio completo (FQDN) nome della macchina da in cui è stato avviato. È necessario ridurre al minimo la finestra per evitare qualsiasi accidentale clic del mouse nella finestra di come modificare lo stato attivo viene sospeso tutte le altre azioni.

## <a name="next-steps"></a>Passaggi successivi

- [Convalidare una nuova soluzione di Azure Stack](azure-stack-vaas-validate-solution-new.md)  
- Se si dispone di connettività Internet lenta o intermittenti, è possibile scaricare l'immagine del repository. Per altre informazioni, vedere [gestiscono la connettività di rete lenta](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity).
- Per altre informazioni sulle [convalida Azure Stack come servizio](https://docs.microsoft.com/azure/azure-stack/partner).
