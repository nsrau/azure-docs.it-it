---
title: Panoramica dell'agente Connected Machine per Windows
description: Questo articolo fornisce una panoramica dettagliata dell'agente server abilitati per Azure Arc, che supporta il monitoraggio di macchine virtuali ospitate in ambienti ibridi.
ms.date: 09/30/2020
ms.topic: conceptual
ms.openlocfilehash: f1f74ff12d007553c0c0c9b16f56a27371618bbb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370169"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Panoramica dell'agente di server abilitati per Azure Arc

Azure Arc Enabled Servers Connected Machine Agent consente di gestire i computer Windows e Linux ospitati all'esterno di Azure nella rete aziendale o in un altro provider di servizi cloud. Questo articolo offre una panoramica dettagliata dell'agente, dei requisiti di sistema e di rete, nonché dei diversi metodi di distribuzione.

>[!NOTE]
>A partire dalla versione generale di Azure Arc Enabled Servers nel 2020 settembre, tutte le versioni non definitive dell'agente del computer connesso di Azure (gli agenti con versioni inferiori a 1,0) verranno **deprecate** entro il **2 febbraio 2021**.  Questo intervallo di tempo consente di eseguire l'aggiornamento alla versione 1,0 o successiva prima che gli agenti pre-rilasciati non siano più in grado di comunicare con il servizio server abilitati per Azure Arc.

## <a name="agent-component-details"></a>Dettagli del componente agente

Il pacchetto agente computer connesso di Azure contiene diversi componenti logici, che vengono aggregati insieme.

* Il servizio metadati dell'istanza ibrida (HIMDS) gestisce la connessione ad Azure e all'identità di Azure del computer connesso.

* L'agente di configurazione Guest fornisce funzionalità di configurazione dei criteri e Guest di In-Guest, ad esempio la valutazione della conformità del computer ai criteri richiesti.

    Si noti il comportamento seguente con la [configurazione Guest](../../governance/policy/concepts/guest-configuration.md) di criteri di Azure per un computer disconnesso:

    * Un'assegnazione dei criteri di configurazione Guest destinata A computer disconnessi non è interessata.
    * L'assegnazione Guest viene archiviata localmente per 14 giorni. Nel periodo di 14 giorni, se l'agente del computer connesso si riconnette al servizio, verranno riapplicate le assegnazioni dei criteri.
    * Le assegnazioni vengono eliminate dopo 14 giorni e non vengono riassegnate al computer dopo il periodo di 14 giorni.

* L'agente di estensione gestisce le estensioni della macchina virtuale, tra cui installazione, disinstallazione e aggiornamento. Le estensioni vengono scaricate da Azure e copiate nella `%SystemDrive%\AzureConnectedMachineAgent\ExtensionService\downloads` cartella in Windows e per Linux in `/opt/GC_Ext/downloads` . In Windows, l'estensione viene installata nel percorso seguente `%SystemDrive%\Packages\Plugins\<extension>` e in Linux l'estensione viene installata in `/var/lib/waagent/<extension>` .

## <a name="download-agents"></a>Scaricare agenti

È possibile scaricare il pacchetto dell'agente Azure Connected Machine per Windows e Linux dai percorsi elencati di seguito.

* [Pacchetto di installazione dell'agente Connected Machine per Windows](https://aka.ms/AzureConnectedMachineAgent) dall'Area download Microsoft.

* Il pacchetto dell'agente per Linux viene distribuito dal [repository dei pacchetti](https://packages.microsoft.com/) di Microsoft usando il formato di pacchetto preferito per la distribuzione (RPM o DEB).

L'agente Azure Connected Machine per Windows e Linux può essere aggiornato alla versione più recente manualmente o automaticamente in base alle esigenze. Per altre informazioni, vedere [qui](manage-agent.md).

## <a name="prerequisites"></a>Prerequisiti

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

Le versioni seguenti dei sistemi operativi Windows e Linux sono ufficialmente supportate per l'agente Azure Connected Machine:

- Windows Server 2012 R2 e versioni successive (incluso Windows Server Core)
- Ubuntu 16,04 e 18,04 LTS (x64)
- CentOS Linux 7 (x64)
- SUSE Linux Enterprise Server (SLES) 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

### <a name="required-permissions"></a>Autorizzazioni necessarie

* Per eseguire l'onboarding di computer, è necessario essere membri del ruolo **Onboarding di computer connessi di Azure**.

* Per leggere, modificare, onboarding ed eliminare un computer, si è membri del ruolo di **amministratore delle risorse del computer connesso di Azure** . 

### <a name="azure-subscription-and-service-limits"></a>Limiti del servizio e della sottoscrizione di Azure

Prima di configurare i computer con i server abilitati per Azure Arc, esaminare i limiti della [sottoscrizione](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) Azure Resource Manager e dei [gruppi di risorse](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) per pianificare il numero di computer da connettere.

### <a name="transport-layer-security-12-protocol"></a>Protocollo Transport Layer Security 1,2

Per garantire la sicurezza dei dati in transito verso Azure, è consigliabile configurare la macchina per usare il protocollo Transport Layer Security (TLS) 1.2. Le versioni precedenti di TLS/Secure Sockets Layer (SSL) sono state considerate vulnerabili. Nonostante siano ancora attualmente in uso per questioni di compatibilità con le versioni precedenti, **non sono consigliate**.

|Piattaforma/linguaggio | Supporto | Altre informazioni |
| --- | --- | --- |
|Linux | Le distribuzioni Linux si basano generalmente su [OpenSSL](https://www.openssl.org) per supportare TLS 1.2. | Controllare nel [log delle modifiche di OpenSSL](https://www.openssl.org/news/changelog.html) per assicurarsi che la versione di OpenSSL sia supportata.|
| Windows Server 2012 R2 e versioni successive | Supportato e abilitato per impostazione predefinita. | Assicurarsi che le [impostazioni predefinite](/windows-server/security/tls/tls-registry-settings) siano ancora in uso.|

### <a name="networking-configuration"></a>Configurazione delle impostazioni di rete

L'agente Connected Machine per Linux e Windows comunica in modo sicuro in uscita con Azure Arc sulla porta TCP 443. Se la macchina virtuale si connette tramite un firewall o un server proxy per comunicare in Internet, vedere i requisiti seguenti per comprendere quale configurazione di rete è necessaria.

Se la connettività in uscita è limitata dal firewall o dal server proxy, verificare che gli URL elencati di seguito non siano bloccati. Se si accettano solo gli intervalli IP o i nomi di dominio necessari per la comunicazione dell'agente con il servizio, è necessario anche consentire l'accesso agli URL e ai tag del servizio seguenti.

Tag del servizio:

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure

URL:

| Risorsa dell'agente | Descrizione |
|---------|---------|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`*.guestconfiguration.azure.com` |Configurazione guest|
|`*.his.arc.azure.com`|Servizio ibrido di gestione delle identità|

Gli agenti di anteprima (versione 0,11 e inferiore) richiedono anche l'accesso agli URL seguenti:

| Risorsa dell'agente | Descrizione |
|---------|---------|
|`agentserviceapi.azure-automation.net`|Configurazione guest|
|`*-agentservice-prod-1.azure-automation.net`|Configurazione guest|

Per un elenco degli indirizzi IP per ogni tag del servizio/area, vedere il file JSON [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519) (Indirizzi IP di Azure e tag del servizio - Cloud pubblico). Microsoft pubblica aggiornamenti settimanali contenenti ogni servizio di Azure e gli intervalli IP usati dal servizio. Per altre informazioni, vedere [Tag di servizio](../../virtual-network/network-security-groups-overview.md#service-tags).

Gli URL nella tabella precedente sono necessari in aggiunta alle informazioni relative agli intervalli di indirizzi IP dei tag del servizio, perché la maggior parte dei servizi non dispone attualmente di una registrazione dei tag del servizio e, di conseguenza, gli indirizzi IP sono soggetti a modifiche. Se gli intervalli di indirizzi IP sono necessari per la configurazione del firewall, occorre usare il tag del servizio **AzureCloud** per consentire l'accesso a tutti i servizi di Azure. Non disabilitare il monitoraggio della sicurezza o l'ispezione di questi URL, ma consentire tali URL come si farebbe con il resto del traffico Internet.

### <a name="register-azure-resource-providers"></a>Registrare i provider di risorse di Azure

I server abilitati per Azure Arc dipendono dai provider di risorse di Azure seguenti nella sottoscrizione per poter usare questo servizio:

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

Se non sono registrati, è possibile registrarli con i comandi seguenti:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Interfaccia della riga di comando di Azure:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

È anche possibile registrare i provider di risorse nel portale seguendo la procedura descritta nel [portale di Azure](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="installation-and-configuration"></a>Installazione e configurazione

La connessione delle macchine virtuali nell'ambiente ibrido direttamente con Azure può essere eseguita usando metodi diversi a seconda dei requisiti. La tabella seguente illustra ogni metodo per determinare quello più adatto alla propria organizzazione.

> [!IMPORTANT]
> Non è possibile installare l'agente del computer connesso in una macchina virtuale Windows di Azure. Se si tenta di eseguire questa operazione, viene rilevata l'installazione e viene eseguito il rollback.

| Metodo | Descrizione |
|--------|-------------|
| In modo interattivo | Installare manualmente l'agente in una singola o in un numero limitato di macchine virtuali seguendo i passaggi descritti in [Connettere i computer ad Azure con Azure Arc per server - Portale](onboard-portal.md).<br> Dal portale di Azure è possibile generare uno script ed eseguirlo sulla macchina virtuale per automatizzare i passaggi di installazione e configurazione dell'agente.|
| Su larga scala | Installare e configurare l'agente per più macchine virtuali seguendo le procedure in [Connettere computer ad Azure con Azure Arc per server - PowerShell](onboard-service-principal.md).<br> Questo metodo crea un'entità servizio per connettere le macchine virtuali in modo non interattivo.|
| Su larga scala | Installare e configurare l'agente per più computer seguendo il metodo descritto in [Uso della piattaforma DSC di Azure PowerShell](onboard-dsc.md).<br> Questo metodo prevede l'uso di un'entità servizio per connettere i computer in modo non interattivo con DSC di PowerShell. |

## <a name="connected-machine-agent-technical-overview"></a>Panoramica tecnica sull'agente di computer connesso

### <a name="windows-agent-installation-details"></a>Dettagli sull'installazione dell'agente per Windows

È possibile installare l'agente Connected Machine per Windows usando uno dei tre metodi seguenti:

* Facendo doppio clic sul file `AzureConnectedMachineAgent.msi`.
* Manualmente eseguendo il pacchetto di Windows Installer `AzureConnectedMachineAgent.msi` dalla shell dei comandi.
* Da una sessione di PowerShell usando un metodo con script.

Dopo l'installazione dell'agente Connected Machine per Windows, vengono applicate le modifiche di configurazione aggiuntive seguenti a livello di sistema.

* Durante l'installazione, vengono create le cartelle di installazione seguenti.

    |Cartella |Descrizione |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |Percorso di installazione predefinito che contiene i file di supporto dell'agente.|
    |%ProgramData%\AzureConnectedMachineAgent |Contiene i file di configurazione dell'agente.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |Contiene i token acquisiti.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |Contiene il file di configurazione dell'agente `agentconfig.json`, che documenta le informazioni di registrazione con il servizio.|
    |Files\ArcConnectedMachineAgent\ExtensionService\GC%SystemDrive%\Program | Percorso di installazione contenente i file dell'agente di configurazione Guest. |
    |%ProgramData%\GuestConfig |Contiene i criteri applicati da Azure.|
    |%SystemDrive%\AzureConnectedMachineAgent\ExtensionService\downloads | Le estensioni vengono scaricate da Azure e copiate qui.|

* I servizi Windows seguenti vengono creati nel computer di destinazione durante l'installazione dell'agente.

    |Nome servizio |Nome visualizzato |Nome del processo |Descrizione |
    |-------------|-------------|-------------|------------|
    |himds |Servizio metadati dell'istanza di Azure Hybrid |himds.exe |Questo servizio implementa il servizio metadati dell'istanza di Azure (IMDS) per gestire la connessione ad Azure e l'identità di Azure del computer connesso.|
    |DscService |Servizio di configurazione guest |dsc_service.exe |La codebase DSC (Desired state Configuration) usata all'interno di Azure per implementare i criteri di In-Guest.|

* Durante l'installazione dell'agente, vengono create le variabili di ambiente seguenti.

    |Nome |Valore predefinito |Descrizione |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Sono disponibili diversi file di log per la risoluzione dei problemi. descritti nella tabella seguente.

    |File di log |Descrizione |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Registra informazioni dettagliate sul servizio agenti (HIMDS) e sull'interazione con Azure.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Contiene l'output dei comandi dello strumento azcmagent, quando viene usato l'argomento verbose (-v).|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent.log |Registra informazioni dettagliate sull'attività del servizio DSC,<br> in particolare la connettività tra il servizio HIMDS e i criteri di Azure.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |Registra informazioni dettagliate sulla telemetria del servizio DSC e la registrazione dettagliata.|
    |Ext_mgr_logs%SystemDrive%\ProgramData\GuestConfig\|Registra informazioni dettagliate sul componente agente di estensione.|
    |Extension_logs%SystemDrive%\ProgramData\GuestConfig\\<Extension>|Registra i dettagli dall'estensione installata.|

* Viene creato il gruppo di sicurezza locale **applicazioni di estensione dell'agente ibrido**.

* Durante la disinstallazione dell'agente, non vengono rimossi gli artefatti seguenti.

    * *%ProgramData%\AzureConnectedMachineAgent\Log
    * %ProgramData%\AzureConnectedMachineAgent e sottodirectory
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Dettagli sull'installazione dell'agente per Linux

L'agente Connected Machine per Linux viene fornito nel formato di pacchetto preferito per la distribuzione (RPM o DEB) ospitata nel [repository dei pacchetti](https://packages.microsoft.com/) di Microsoft. L'agente viene installato e configurato con il bundle di script della shell [Install_linux_azcmagent.sh](https://aka.ms/azcmagent).

Dopo l'installazione dell'agente Connected Machine per Linux, vengono applicate le modifiche di configurazione aggiuntive seguenti a livello di sistema.

* Durante l'installazione, vengono create le cartelle di installazione seguenti.

    |Cartella |Descrizione |
    |-------|------------|
    |/var/opt/azcmagent/ |Percorso di installazione predefinito che contiene i file di supporto dell'agente.|
    |/opt/azcmagent/ |
    |GC_Ext/opt/ | Percorso di installazione contenente i file dell'agente di configurazione Guest.|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |Contiene i token acquisiti.|
    |/var/lib/GuestConfig |Contiene i criteri applicati da Azure.|
    |/opt/GC_Ext/downloads|Le estensioni vengono scaricate da Azure e copiate qui.|

* I daemon seguenti vengono creati nel computer di destinazione durante l'installazione dell'agente.

    |Nome servizio |Nome visualizzato |Nome del processo |Descrizione |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Servizio metadati dell'istanza di Azure Hybrid |/opt/azcmagent/bin/himds |Questo servizio implementa il servizio metadati dell'istanza di Azure (IMDS) per gestire la connessione ad Azure e l'identità di Azure del computer connesso.|
    |dscd.service |Servizio di configurazione guest |/opt/DSC/dsc_linux_service |Si tratta della codebase Desired State Configuration (DSC v2) usata in Azure per implementare i criteri nel guest.|

* Sono disponibili diversi file di log per la risoluzione dei problemi. descritti nella tabella seguente.

    |File di log |Descrizione |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Registra informazioni dettagliate sul servizio agenti (HIMDS) e sull'interazione con Azure.|
    |/var/opt/azcmagent/log/azcmagent.log |Contiene l'output dei comandi dello strumento azcmagent, quando viene usato l'argomento verbose (-v).|
    |/opt/logs/dsc.log |Registra informazioni dettagliate sull'attività del servizio DSC,<br> in particolare la connettività tra il servizio himds e i criteri di Azure.|
    |/opt/logs/dsc.telemetry.txt |Registra informazioni dettagliate sulla telemetria del servizio DSC e la registrazione dettagliata.|
    |ext_mgr_logs/var/lib/GuestConfig/ |Registra informazioni dettagliate sul componente agente di estensione.|
    |extension_logs/var/lib/GuestConfig/|Registra i dettagli dall'estensione installata.|

* Durante l'installazione dell'agente, vengono create le variabili di ambiente seguenti. Queste variabili vengono impostate in `/lib/systemd/system.conf.d/azcmagent.conf`.

    |Nome |Valore predefinito |Descrizione |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Durante la disinstallazione dell'agente, non vengono rimossi gli artefatti seguenti.

    * /var/opt/azcmagent
    * /opt/logs

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a valutare i server abilitati per Azure Arc, seguire l'articolo [connettere macchine ibride ad Azure dalla portale di Azure](onboard-portal.md).

* Le informazioni sulla risoluzione dei problemi sono reperibili nella [Guida alla risoluzione dei problemi relativi all'agente del computer connesso](troubleshoot-agent-onboard.md).