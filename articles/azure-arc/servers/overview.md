---
title: Panoramica di Azure Arc per server (anteprima)
description: Questo articolo illustra come usare Azure Arc per server per gestire macchine virtuali ospitate all'esterno di Azure come se fossero risorse di Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: automazione di azure, DSC, powershell, configurazione dello stato desiderato, gestione aggiornamenti, rilevamento modifiche, inventario, runbook, python, grafico, ibrido
ms.date: 01/29/2020
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: b0f1d235391c4c4e3804a6dccc8174e946035b6a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899193"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Che cos'è Azure Arc per server (anteprima)

Azure Arc per server (anteprima) consente di gestire le macchine virtuali Windows e Linux ospitate all'esterno di Azure nella rete aziendale o in un altro provider di servizi cloud, in modo analogo a come si gestiscono le macchine virtuali native di Azure. Quando una macchina virtuale ibrida viene connessa ad Azure, diventa una macchina virtuale connessa e viene considerata come una risorsa in Azure. Ogni macchina virtuale connessa dispone di un ID risorsa, viene gestita come parte di un gruppo di risorse all'interno di una sottoscrizione e usufruisce dei vantaggi derivanti dai costrutti di Azure standard, ad esempio Criteri di Azure e assegnazione di tag.

Per offrire questa esperienza con le macchine virtuali ibride ospitate all'esterno di Azure, è necessario installare l'agente Azure Connected Machine in ogni macchina virtuale che si prevede di connettere ad Azure. Questo agente non fornisce altre funzionalità e non sostituisce l'[agente di Azure Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si vuole monitorare in modo proattivo il sistema operativo e i carichi di lavoro in esecuzione nella macchina virtuale, gestirla con runbook di automazione o soluzioni come Gestione aggiornamenti o usare altri servizi di Azure, come il [Centro sicurezza di Azure](../../security-center/security-center-intro.md).

>[!NOTE]
>Questa versione di anteprima è destinata a scopi di valutazione e non è consigliabile usarla per gestire computer di produzione critici.
>

## <a name="supported-scenarios"></a>Scenari supportati

Azure Arc per server (anteprima) supporta gli scenari seguenti con macchine virtuali connesse:

- Assegnare [configurazioni guest di Criteri di Azure](../../governance/policy/concepts/guest-configuration.md) usando la stessa esperienza dell'assegnazione dei criteri per le macchine virtuali di Azure.
- I dati di log raccolti dall'agente di Log Analytics e archiviati nell'area di lavoro di Log Analytics in cui è registrata la macchina virtuale contengono ora proprietà specifiche della macchina virtuale, come l'ID risorsa, che possono essere usate per supportare l'accesso ai log di tipo [risorsa-contesto](../../azure-monitor/platform/design-logs-deployment.md#access-mode).

## <a name="supported-regions"></a>Aree supportate

Con Azure Arc per server (anteprima) sono supportate solo alcune aree:

- Stati Uniti occidentali 2
- Europa occidentale
- Asia occidentale

## <a name="prerequisites"></a>Prerequisites

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

Le versioni seguenti dei sistemi operativi Windows e Linux sono ufficialmente supportate per l'agente Azure Connected Machine: 

- Windows Server 2012 R2 e versioni successive
- Ubuntu 16.04 e 18.04

>[!NOTE]
>Questa versione di anteprima dell'agente Connected Machine per Windows supporta solo Windows Server configurato per l'uso della lingua inglese.
>

### <a name="azure-subscription-and-service-limits"></a>Limiti del servizio e della sottoscrizione di Azure

Prima di configurare le macchine virtuali con Azure Arc per server (anteprima), è consigliabile esaminare i [limiti della sottoscrizione](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits---azure-resource-manager) di Azure Resource Manager e i [limiti del gruppo di risorse](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) per pianificare il numero di macchine virtuali da connettere.

### <a name="networking-configuration"></a>Configurazione delle impostazioni di rete

L'agente Connected Machine per Linux e Windows comunica in modo sicuro in uscita con Azure Arc sulla porta TCP 443. Se la macchina virtuale si connette tramite un firewall o un server proxy per comunicare in Internet, vedere i requisiti seguenti per comprendere quale configurazione di rete è necessaria.

Se la connettività in uscita è limitata dal firewall o dal server proxy, verificare che gli URL elencati di seguito non siano bloccati. Se si accettano solo gli intervalli IP o i nomi di dominio necessari per la comunicazione dell'agente con il servizio, è necessario anche consentire l'accesso agli URL e ai tag del servizio seguenti.

Tag del servizio:

- AzureActiveDirectory
- AzureTrafficManager

URL:

| Risorsa dell'agente | Descrizione |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Configurazione guest|
|*-agentservice-prod-1.azure-automation.net|Configurazione guest|
|*.his.hybridcompute.azure-automation.net|Servizio ibrido di gestione delle identità|

Per un elenco degli indirizzi IP per ogni tag del servizio/area, vedere il file JSON [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519) (Indirizzi IP di Azure e tag del servizio - Cloud pubblico). Microsoft pubblica aggiornamenti settimanali contenenti ogni servizio di Azure e gli intervalli IP usati dal servizio. Per altre informazioni, vedere [Tag di servizio](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Gli URL nella tabella precedente sono necessari in aggiunta alle informazioni relative agli intervalli di indirizzi IP dei tag del servizio, perché la maggior parte dei servizi non dispone attualmente di una registrazione dei tag del servizio e, di conseguenza, gli indirizzi IP sono soggetti a modifiche. Se gli intervalli di indirizzi IP sono necessari per la configurazione del firewall, occorre usare il tag del servizio **AzureCloud** per consentire l'accesso a tutti i servizi di Azure. Non disabilitare il monitoraggio della sicurezza o l'ispezione di questi URL, ma consentire tali URL come si farebbe con il resto del traffico Internet.

### <a name="register-azure-resource-providers"></a>Registrare i provider di risorse di Azure

Azure Arc per server (anteprima) dipende dai provider di risorse di Azure seguenti nella sottoscrizione per poter usare questo servizio:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Se non sono registrati, è possibile registrarli con i comandi seguenti:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Interfaccia della riga di comando di Azure:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

È anche possibile registrare i provider di risorse nel portale seguendo la procedura descritta nel [portale di Azure](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="connected-machine-agent"></a>Agente Connected Machine

È possibile scaricare il pacchetto dell'agente Azure Connected Machine per Windows e Linux dai percorsi elencati di seguito.

- [Pacchetto di installazione dell'agente Connected Machine per Windows](https://aka.ms/AzureConnectedMachineAgent) dall'Area download Microsoft.
- Il pacchetto dell'agente per Linux viene distribuito dal [repository dei pacchetti](https://packages.microsoft.com/) di Microsoft usando il formato di pacchetto preferito per la distribuzione (RPM o DEB).

>[!NOTE]
>Durante questa anteprima è stato rilasciato un solo pacchetto, adatto per Ubuntu 16.04 o 18.04.

## <a name="install-and-configure-agent"></a>Installare e configurare l'agente

La connessione delle macchine virtuali nell'ambiente ibrido direttamente con Azure può essere eseguita usando metodi diversi a seconda dei requisiti. La tabella seguente illustra ogni metodo per determinare quello più adatto alla propria organizzazione.

| Metodo | Descrizione |
|--------|-------------|
| In modo interattivo | Installare manualmente l'agente in una singola o in un numero limitato di macchine virtuali seguendo i passaggi descritti in [Connettere i computer ad Azure con Azure Arc per server - Portale](quickstart-onboard-portal.md).<br> Dal portale di Azure è possibile generare uno script ed eseguirlo sulla macchina virtuale per automatizzare i passaggi di installazione e configurazione dell'agente.|
| Su larga scala | Installare e configurare l'agente per più macchine virtuali seguendo le procedure in [Connettere computer ad Azure con Azure Arc per server - PowerShell](quickstart-onboard-powershell.md).<br> Questo metodo crea un'entità servizio per connettere le macchine virtuali in modo non interattivo.|


## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a valutare Azure Arc per server (anteprima), vedere l'articolo [Connettere macchine virtuali ibride ad Azure dal portale di Azure](quickstart-onboard-portal.md). 