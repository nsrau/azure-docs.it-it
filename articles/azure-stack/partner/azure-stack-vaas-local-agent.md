---
title: Distribuire l'agente locale | Microsoft Docs
description: Distribuire l'agente locale per la convalida di Azure Stack come servizio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 53309eceaab566836cef5f73dff2b69db5d9c135
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55102414"
---
# <a name="deploy-the-local-agent"></a>Distribuire l'agente locale

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Informazioni su come usare la convalida come un agente locale del servizio (VaaS) per controllare l'hardware. La soluzione di Azure Stack in fase di convalida prima di eseguire il test di convalida occorre installarvi l'agente locale.

> [!Note]  
> È necessario assicurarsi che la macchina in cui viene eseguito l'agente locale non perdita l'accesso uscita a internet. Questo computer deve essere accessibile solo agli utenti che si sono autorizzati a usare VaaS per conto del tenant.

Per distribuire l'agente locale:

1. Installare l'agente locale
2. Eseguire i controlli di integrità
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
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "OnPremAgent.zip"
    Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent -Force
    Set-Location VaaSOnPremAgent\lib\net46
    ```

3. Eseguire il comando seguente per installare le dipendenze dell'agente locale:

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

    **Parameters**

    | Parametro | DESCRIZIONE |
    | --- | --- |
    | aadServiceAdminUser | L'utente amministratore globale per il tenant di Azure AD. Ad esempio potrebbe essere, vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | La password per l'utente amministratore globale. |
    | AadTenantId | ID tenant di Azure AD per l'account di Azure registrati con la convalida come servizio. |
    | ExternalFqdn | È possibile ottenere il nome di dominio completo del file di configurazione. Per istruzioni, vedere [parametri comuni del flusso di lavoro in Azure Stack di convalida come servizio](azure-stack-vaas-parameters.md). |
    | Region | L'area del tenant di Azure AD. |

Il comando Scarica un'immagine del repository (repository) immagine pubblica (file VHD del sistema operativo) e copiare da un archivio blob di Azure nella risorsa di archiviazione di Azure Stack.

![Scarica prerequisiti](media/installingprereqs.png)

> [!Note]
> Se si verificano velocità di rete lenta durante il download di queste immagini, eseguirne il download separatamente in una condivisione locale e specificare il parametro **- LocalPackagePath** *FileShareOrLocalPath*. È possibile trovare ulteriori indicazioni durante il download del repository nella sezione [gestiscono la connettività di rete lenta](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) dei [risolvere i problemi di convalida come servizio](azure-stack-vaas-troubleshoot.md).

## <a name="checks-before-starting-the-tests"></a>Controlla prima di avviare i test

L'esecuzione di azioni remote dei test. Il computer che esegue il test deve avere accesso agli endpoint di Azure Stack, in caso contrario, i test non funziona. Se si usa l'agente locale VaaS, usare la macchina in cui verrà eseguito l'agente. È possibile verificare che il computer abbia accesso agli endpoint di Azure Stack, eseguire i controlli seguenti:

1. Controllare che l'URI di Base può essere raggiunto. Aprire un prompt dei comandi o bash shell ed eseguire il seguente comando, sostituendo `<EXTERNALFQDN>` con il nome di dominio completo esterna dell'ambiente:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Aprire un web browser e passare a `https://adminportal.<EXTERNALFQDN>` per controllare che il portale di agenti di gestione può essere raggiunto.

3. Accedere con Azure AD del servizio i valori di nome e la password amministratore specificati durante la creazione il superamento del test.

4. Verificare l'integrità del sistema eseguendo il **Test-AzureStack** cmdlet di PowerShell come descritto in [eseguire un test di convalida per Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test). Risolvere eventuali avvisi ed errori prima di avviare i test.

## <a name="run-the-agent"></a>Eseguire l'agente

1. Aprire Windows PowerShell in un prompt dei comandi con privilegi elevati.

2. Eseguire il comando seguente:

    ```PowerShell
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ```

      **Parameters**  
    | Parametro | DESCRIZIONE |
    | --- | --- |
    | VaaSUserId | ID utente usato per accedere al portale VaaS (ad esempio, UserName@Contoso.com) |
    | VaaSTenantId | ID tenant di Azure AD per l'account di Azure registrati con la convalida come servizio. |

    > [!Note]  
    > Quando si esegue l'agente, la directory di lavoro corrente deve essere il percorso del file eseguibile, l'host del motore attività **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Se non viene visualizzato di eventuali errori rilevati, l'agente locale è stata completata. Il testo di esempio seguente viene visualizzato nella finestra della console.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Agente avviato](media/startedagent.png)

Un agente viene identificato in base al relativo nome. Per impostazione predefinita, Usa il nome di dominio completo (FQDN) nome della macchina da in cui è stato avviato. È necessario ridurre al minimo la finestra per evitare eventuali istruzioni SELECT accidentale nella finestra di come modificare lo stato attivo viene sospeso tutte le altre azioni.

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere i problemi di convalida come servizio](azure-stack-vaas-troubleshoot.md)
- [Convalida di un concetti chiave di servizio](azure-stack-vaas-key-concepts.md)
- [Guida introduttiva: Usare la convalida di un portale del servizio per pianificare il primo test](azure-stack-vaas-schedule-test-pass.md)