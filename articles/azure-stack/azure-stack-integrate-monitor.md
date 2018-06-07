---
title: Integrare la soluzione di monitoraggio esterna con lo Stack di Azure | Documenti Microsoft
description: Informazioni su come integrare Azure Stack con una soluzione di monitoraggio esterna nel Data Center.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/10/2018
ms.author: jeffgilb
ms.reviewer: thoroet
ms.openlocfilehash: d7c8520602132722fd0c7138de4a276b9ac2208a
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807340"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrare la soluzione di monitoraggio esterna con lo Stack di Azure

Per il monitoraggio esterno dell'infrastruttura di Azure Stack, è necessario monitorare il software di Stack di Azure, i computer fisici e i commutatori di rete fisica. Ognuna di queste aree offre un metodo per recuperare le informazioni di stato e di avviso:

- Il software di Stack Azure offre un'API basata su REST per recuperare l'integrità e avvisi. L'utilizzo delle tecnologie definita dal software, ad esempio spazi di archiviazione diretta, lo stato di archiviazione e avvisi fanno parte del software di monitoraggio.
- Computer fisico può rendere disponibile tramite i baseboard management controller (BMC) l'integrità e informazioni sugli avvisi.
- I dispositivi di rete fisica possono rendere disponibile tramite il protocollo SNMP l'integrità e informazioni sugli avvisi.

Ogni soluzione Azure Stack viene fornito con un host del ciclo di vita di hardware. L'host esegue software di monitoraggio per i server fisici e i dispositivi di rete del fornitore di hardware Original Equipment Manufacturer (OEM). Se si desidera, è possibile ignorare queste soluzioni di monitoraggio e integrare direttamente con le soluzioni esistenti di monitoraggio nel Data Center.

> [!IMPORTANT]
> La soluzione di monitoraggio esterna che è utilizzare deve essere senza agente. È possibile installare agenti di terze parti all'interno dei componenti dello Stack di Azure.

Il diagramma seguente mostra il flusso del traffico tra un sistema integrato dello Stack di Azure, l'host del ciclo di vita di hardware, una soluzione di monitoraggio esterna e un sistema di raccolta di emissione/dati esterni.

![Diagramma che illustra il traffico tra Azure Stack, il monitoraggio e il ticket soluzione.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

In questo articolo viene illustrato come integrare Azure Stack con soluzioni di monitoraggio esterne, ad esempio System Center Operations Manager e Nagios. Include inoltre come utilizzare gli avvisi a livello di programmazione tramite PowerShell o tramite chiamate all'API REST.

## <a name="integrate-with-operations-manager"></a>Integrazione con Operations Manager

È possibile utilizzare Operations Manager per il monitoraggio esterno dello Stack di Azure. System Center Management Pack per Microsoft Azure Stack consente di monitorare più distribuzioni di Azure Stack con una singola istanza di Operations Manager. Il management pack utilizza il provider di risorse di integrità e il provider di risorse aggiornamento API REST per comunicare con lo Stack di Azure. Se si prevede di ignorare OEM software che è in esecuzione nell'host hardware del ciclo di vita di monitoraggio, è possibile installare i management pack fornitore per monitorare i server fisici. È anche possibile utilizzare Individuazione dei dispositivi di rete Operations Manager per monitorare i commutatori di rete.

Il management pack per lo Stack di Azure offre le funzionalità seguenti:

- È possibile gestire più distribuzioni di Azure Stack
- Supporto per Azure Active Directory (Azure AD) e Active Directory Federation Services (ADFS)
- È possibile recuperare e chiudere gli avvisi
- È presente dello stato e un dashboard di capacità
- Include il rilevamento di modalità di manutenzione automatica per l'utilizzo di patch e aggiornamenti (P & U) in corso
- Include le attività di aggiornamento imposto per la distribuzione e di area
- È possibile aggiungere informazioni personalizzate a un'area
- Supporta la notifica e creazione di report

È possibile scaricare System Center Management Pack per Microsoft Azure Stack e tale [manuale dell'utente](https://www.microsoft.com/en-us/download/details.aspx?id=55184), o direttamente da Operations Manager.

Per una soluzione di creazione di ticket, è possibile integrare Operations Manager con System Center Service Manager. Il connettore prodotto integrato consente la comunicazione bidirezionale che consente di chiudere un avviso nello Stack di Azure e Operations Manager dopo avere risolto una richiesta di servizio in Service Manager.

Il diagramma seguente mostra l'integrazione di Azure Stack con una distribuzione esistente di System Center. È possibile automatizzare Service Manager ulteriormente con System Center Orchestrator o Service Management Automation (SMA) per eseguire operazioni nello Stack di Azure.

![Diagramma che mostra l'integrazione con Operations Manager, Service Manager e SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrazione con Nagios

È stato sviluppato un Nagios plug-in di monitoraggio e le relative soluzioni Cloudbase il partner, disponibile con la licenza permissiva software gratuito: MIT (Massachusetts Institute of Technology).

Il plug-in viene scritto in Python e sfrutta il provider di risorse integrità API REST. Offre funzionalità di base per recuperare e chiudere gli avvisi nello Stack di Azure. Ad esempio il management pack di System Center, consente di aggiungere più distribuzioni di Stack di Azure e per inviare notifiche.

Il plug-in funziona con Nagios Enterprise e Nagios Core. È possibile scaricarlo [qui](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Il sito di download include inoltre i dettagli di installazione e configurazione.

### <a name="plugin-parameters"></a>Parametri di plug-in

Configurare il file di plug-in "Azurestack_plugin.py" con i parametri seguenti:

| Parametro | DESCRIZIONE | Esempio |
|---------|---------|---------|
| *arm_endpoint* | Endpoint di Azure Resource Manager (amministratore) |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Endpoint di Azure Resource Manager (amministratore)  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | ID sottoscrizione Admin | Recuperare tramite il portale dell'amministratore o PowerShell |
| *USER_NAME* | Nome utente di sottoscrizione (operatore) | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Password di sottoscrizione (operatore) | mypassword |
| *Client_id* | Client | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Nome dell'area Azure Stack | local |
|  |  |

* Il GUID di PowerShell fornita è universale. È possibile utilizzarlo per ogni distribuzione.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Utilizzare PowerShell per avvisi e monitoraggio dell'integrità

Se non si utilizza Operations Manager, Nagios o una soluzione basata su Nagios, è possibile utilizzare PowerShell per abilitare una vasta gamma di soluzioni per l'integrazione con Azure Stack di monitoraggio.

1. Per usare PowerShell, assicurarsi di aver [PowerShell installato e configurato](azure-stack-powershell-configure-quickstart.md) per un ambiente di operatore dello Stack di Azure. Installare PowerShell in un computer locale che può raggiungere l'endpoint di gestione risorse (amministratore) (https://adminmanagement. [ Region]. [FQDN_esterno]).

2. Eseguire i comandi seguenti per connettersi all'ambiente dello Stack di Azure come un operatore di Stack di Azure:

   ```PowerShell  
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Utilizzare i comandi come negli esempi seguenti per gestire gli avvisi:
   ```PowerShell
    #Retrieve all alerts
    Get-AzsAlert

    #Filter for active alerts
    $Active=Get-AzsAlert | Where {$_.State -eq "active"}
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    Get-AzsRPHealth

    #Retrieve infrastructure role instance health
    $FRPID=Get-AzsRPHealth|Where-Object {$_.DisplayName -eq "Capacity"}
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId

    ```

## <a name="learn-more"></a>Altre informazioni

Per informazioni sul monitoraggio dello stato incorporate, vedere [monitorare l'integrità e avvisi in Azure Stack](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Passaggi successivi

[Integrazione della sicurezza](azure-stack-integrate-security.md)
