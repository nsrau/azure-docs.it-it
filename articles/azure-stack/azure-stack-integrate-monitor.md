---
title: Integrare la soluzione di monitoraggio esterna con Azure Stack | Microsoft Docs
description: Descrive come integrare Azure Stack con una soluzione di monitoraggio esterna nel tuo Data Center.
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
ms.date: 02/06/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: 64a31e0c8a36b7ea8b60f65caefba9ba15b91777
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258735"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrare la soluzione di monitoraggio esterna con Azure Stack

Per il monitoraggio esterno dell'infrastruttura di Azure Stack, è necessario monitorare il software Azure Stack, i computer fisici e i commutatori di rete fisica. Ognuna di queste aree offre un metodo per recuperare informazioni su integrità e di avviso:

- Software Azure Stack offre un'API basata su REST per recuperare l'integrità e gli avvisi. L'uso di tecnologie definita dal software, ad esempio spazi di archiviazione diretta, lo stato di archiviazione e avvisi fanno parte del software di monitoraggio.
- Computer fisici possono fornire integrità e le informazioni sugli avvisi tramite i baseboard management controller (BMC).
- I dispositivi di rete fisica possono rendere disponibile tramite il protocollo SNMP l'integrità e le informazioni sugli avvisi.

Ogni soluzione di Azure Stack viene fornito con un host del ciclo di vita dell'hardware. L'host esegue software di monitoraggio del fornitore hardware Original Equipment Manufacturer (OEM) per server fisici e dei dispositivi di rete. Se si desidera, è possibile ignorare le soluzioni di monitoraggio e integrare direttamente con le soluzioni esistenti di monitoraggio nel Data Center.

> [!IMPORTANT]
> La soluzione di monitoraggio esterna che è utilizzare deve essere senza agente. Non è possibile installare agenti di terze parti all'interno dei componenti di Azure Stack.

Il diagramma seguente mostra il flusso del traffico tra un sistema integrato Azure Stack, l'host del ciclo di vita dell'hardware, una soluzione di monitoraggio esterna e un sistema di raccolta per le richieste o dati esterni.

![Diagramma che mostra il traffico tra Azure Stack, il monitoraggio e creazione di ticket soluzione.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> Integrazione di monitoraggio esterni direttamente con i dispositivi di rete e server fisici non è consentita e bloccata attivamente da elenchi di controllo di accesso (ACL). 

Questo articolo illustra come integrare Azure Stack con soluzioni di monitoraggio esterne, ad esempio System Center Operations Manager e Nagios. Include inoltre come utilizzare gli avvisi a livello di codice tramite PowerShell o tramite le chiamate API REST.

## <a name="integrate-with-operations-manager"></a>Integrazione con Operations Manager

È possibile utilizzare Operations Manager per il monitoraggio esterno di Azure Stack. System Center Management Pack per Microsoft Azure Stack consente di monitorare più distribuzioni di Azure Stack con una singola istanza di Operations Manager. Il management pack utilizza il provider di risorse di integrità e il provider di risorse Update le API REST per comunicare con Azure Stack. Se si prevede di ignorare l'OEM software che è in esecuzione nell'host del ciclo di vita dell'hardware per il monitoraggio, è possibile installare i management pack fornitore per monitorare i server fisici. È anche possibile usare individuazione dei dispositivi di rete Operations Manager per monitorare i commutatori di rete.

Il management pack per Azure Stack offre le funzionalità seguenti:

- È possibile gestire più distribuzioni di Azure Stack
- È il supporto per Azure Active Directory (Azure AD) e Active Directory Federation Services (ADFS)
- È possibile recuperare e chiudere gli avvisi
- È presente dell'integrità e un dashboard di capacità
- Include il rilevamento di modalità di manutenzione automatica per l'utilizzo di patch e aggiornamento (P & U) in corso
- Include le attività di aggiornamento imposto per la distribuzione e l'area
- È possibile aggiungere informazioni personalizzate in un'area
- Supporta la notifica e creazione di report

È possibile scaricare System Center Management Pack per Microsoft Azure Stack e l'oggetto associato [manuale dell'utente](https://www.microsoft.com/en-us/download/details.aspx?id=55184), o direttamente da Operations Manager.

Per una soluzione di creazione di ticket, è possibile integrare Operations Manager con System Center Service Manager. Il connettore di prodotto integrato consente comunicazioni bidirezionali che consente di chiudere un avviso in Operations Manager e Azure Stack dopo avere risolto una richiesta di servizio in Service Manager.

Il diagramma seguente illustra l'integrazione di Azure Stack con una distribuzione esistente di System Center. È possibile automatizzare Service Manager ulteriormente con System Center Orchestrator o Service Management Automation (SMA) per eseguire operazioni in Azure Stack.

![Diagramma che mostra l'integrazione con Operations Manager, Service Manager e SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrare con Nagios

Un Nagios plug-in di monitoraggio è stato sviluppato insieme alle soluzioni Cloudbase partner, che è disponibile con la licenza di software gratuito permissivo – MIT (Massachusetts Institute of Technology).

Il plug-in è scritto in Python e sfrutta il provider di risorse REST API di integrità. Offre funzionalità di base per recuperare e chiudere gli avvisi in Azure Stack. Ad esempio il management pack di System Center, permette di aggiungere più distribuzioni di Azure Stack e inviare notifiche.

Il plug-in funziona con Enterprise di Nagios e Nagios Core. È possibile scaricarlo [qui](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Il sito di download include anche informazioni dettagliate di installazione e configurazione.

### <a name="plugin-parameters"></a>Parametri di plug-in

Configurare il file di plug-in "Azurestack_plugin.py" con i parametri seguenti:

| Parametro | DESCRIZIONE | Esempio |
|---------|---------|---------|
| *arm_endpoint* | Endpoint di Azure Resource Manager (amministratore) |https:\//adminmanagement.local.azurestack.external |
| *api_endpoint* | Endpoint di Azure Resource Manager (amministratore)  | https:\//adminmanagement.local.azurestack.external |
| *Tenant_id* | ID sottoscrizione amministratore | Recuperare tramite il portale per gli amministratori o PowerShell |
| *User_name* | Nome di sottoscrizione utente operatore | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Password di sottoscrizione di operatore | mypassword |
| *Client_id* | Client | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Nome area di Azure Stack | local |
|  |  |

* Il GUID di PowerShell fornito è universale. È possibile usarlo per ogni distribuzione.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Usare PowerShell per monitorare l'integrità e degli avvisi

Se non si usa Operations Manager, Nagios o una soluzione basata su Nagios, è possibile usare PowerShell per abilitare una vasta gamma di monitoraggio delle soluzioni per l'integrazione con Azure Stack.

1. Per usare PowerShell, assicurarsi di aver [PowerShell installato e configurato](azure-stack-powershell-configure-quickstart.md) per un ambiente di operatore di Azure Stack. Installare PowerShell in un computer locale in grado di raggiungere l'endpoint di gestione risorse (amministratore) (https:\//adminmanagement. [ Region]. [FQDN_esterno]).

2. Eseguire i comandi seguenti per connettersi all'ambiente Azure Stack come operatore di Azure Stack:

   ```PowerShell  
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https:\//adminmanagement.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Usare i comandi come negli esempi seguenti per lavorare con gli avvisi:
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

Per informazioni sul monitoraggio dell'integrità predefinite, vedere [monitorare l'integrità e gli avvisi in Azure Stack](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Passaggi successivi

[Integrazione della sicurezza](azure-stack-integrate-security.md)
