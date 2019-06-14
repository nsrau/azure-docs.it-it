---
title: Informazioni su come controllare il contenuto di una macchina virtuale
description: Informazioni su come Criteri di Azure usa la configurazione guest per controllare le impostazioni all'interno di una macchina virtuale di Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: c98229a28f31ff715f252dc3915ca690e99245ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65979519"
---
# <a name="understand-azure-policys-guest-configuration"></a>Comprendere la configurazione guest di Criteri di Azure

Oltre a il controllo e [correzione](../how-to/remediate-resources.md) le risorse di Azure, criteri di Azure possono controllare le impostazioni all'interno di una macchina virtuale. La convalida viene eseguita dall'estensione della configurazione guest e dal client. L'estensione tramite il client convalida le impostazioni quali la configurazione del sistema operativo, la configurazione o la presenza dell'applicazione, le impostazioni di ambiente e altro ancora.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>Estensione e client

Per controllare le impostazioni all'interno di una macchina virtuale, è abilitata un'[estensione macchina virtuale](../../../virtual-machines/extensions/overview.md). L'estensione scarica l'assegnazione dei criteri applicabile e la configurazione corrispondente.

### <a name="register-guest-configuration-resource-provider"></a>Registrare il provider di risorse della configurazione guest

Prima di poter usare la configurazione guest, è necessario registrare il provider di risorse. È possibile eseguire la registrazione nel portale o con PowerShell. Il provider di risorse viene registrato automaticamente se viene eseguita tramite il portale di assegnazione di un criterio di configurazione di Guest.

#### <a name="registration---portal"></a>Registrazione - Portale

Per registrare il provider di risorse per la configurazione guest tramite il portale di Azure, seguire questa procedura:

1. Avviare il portale di Azure e fare clic su **Tutti i servizi**. Cercare e selezionare **Sottoscrizioni**.

1. Trovare e fare clic sulla sottoscrizione per cui si intende abilitare la configurazione guest.

1. Nel menu a sinistra della pagina **Sottoscrizione** fare clic su **Provider di risorse**.

1. Per filtrare o scorrere fino a individuare **Microsoft.GuestConfiguration**, quindi fare clic su **Registra** sulla stessa riga.

#### <a name="registration---powershell"></a>Registrazione - PowerShell

Per registrare il provider di risorse per la configurazione guest tramite PowerShell, eseguire il seguente comando:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>Strumenti di convalida

All'interno della macchina virtuale, il client della configurazione guest usa gli strumenti locali per eseguire il controllo.

La tabella seguente elenca gli strumenti locali usati on ciascun sistema operativo supportato:

|Sistema operativo|Strumento di convalida|Note|
|-|-|-|
|Windows|[Microsoft DSC (Desired State Configuration)](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby e Python vengono installati dall'estensione della configurazione guest. |

### <a name="validation-frequency"></a>Frequenza di convalida

Il client della configurazione guest verifica la presenza di nuovi contenuti ogni cinque minuti. Dopo aver ricevuto un'assegnazione guest, le impostazioni vengono controllate a intervalli di 15 minuti. Al termine del controllo, i risultati vengono inviati al provider di risorse di configurazione guest. Quando vengono applicati criteri di tipo [trigger di valutazione](../how-to/get-compliance-data.md#evaluation-triggers), nel provider di risorse di configurazione guest viene scritto lo stato del computer. In questo modo, Criteri di Azure può valutare le proprietà di Azure Resource Manager. Una valutazione di criteri di Azure on demand recupera il valore più recente dal provider di risorse di configurazione di Guest. ma non attiva un nuovo controllo della configurazione nella macchina virtuale.

### <a name="supported-client-types"></a>Tipi di client supportati

La tabella seguente elenca i sistemi operativi supportati su Immagini di Azure:

|Editore|Name|Versioni|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|Data Center 2012, 2012 R2 Datacenter, 2016 Datacenter, Datacenter 2019|
|Microsoft|Client Windows|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux.|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> Configurazione Guest può controllare i nodi che eseguono un sistema operativo supportato. Se vuoi controllare le macchine virtuali che usano un'immagine personalizzata, è necessario duplicare i **DeployIfNotExists** definizione e modificare le **se** sezione per includere le proprietà dell'immagine.

### <a name="unsupported-client-types"></a>Tipi di client non supportati

Windows Server Nano Server non è supportato in una versione precedente.

### <a name="guest-configuration-extension-network-requirements"></a>Requisiti di rete guest configurazione estensione

Per comunicare con il provider di risorse di configurazione Guest in Azure, macchine virtuali richiedono l'accesso in uscita al Data Center di Azure sulla porta **443**. Se si usa una rete virtuale privata in Azure e non consentire il traffico in uscita, è necessario configurare le eccezioni usando [Network Security Group](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) regole. A questo punto, non esiste un tag di servizio per la configurazione di Guest dei criteri di Azure.

Per un elenco di indirizzi IP, è possibile scaricare [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). Questo file viene aggiornato ogni settimana e presenta gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. È sufficiente consentire l'accesso in uscita agli indirizzi IP nelle aree in cui vengono distribuite le macchine virtuali.

> [!NOTE]
> Il file XML degli indirizzi IP dei data center di Azure elenca gli intervalli di indirizzi IP usati nei data center di Microsoft Azure. Il file include gli intervalli per le risorse di calcolo, SQL e di archiviazione. Viene pubblicata una versione aggiornata del file ogni settimana. Il file include gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. I nuovi intervalli riportati nel file non vengono usati nei data center per almeno una settimana. È consigliabile scaricare il nuovo file XML ogni settimana e aggiornare quindi il sito per identificare correttamente i servizi in esecuzione in Azure. Per gli utenti di ExpressRoute è importante sottolineare che questo file viene usato per aggiornare l'annuncio BGP (Border Gateway Protocol) dello spazio di Azure la prima settimana del mese.

## <a name="guest-configuration-definition-requirements"></a>Requisiti per la definizione della configurazione guest

Ogni controllo eseguito dalla configurazione Guest due definizioni di criteri, è necessario un **DeployIfNotExists** definizione e un **Audit** definizione. Il **DeployIfNotExists** definizione viene utilizzata per preparare la macchina virtuale con l'agente di configurazione Guest e altri componenti per supportare le [strumenti di convalida](#validation-tools).

La definizione dei criteri **DeployIfNotExists** convalida e corregge gli elementi seguenti:

- Conferma che la macchina virtuale sia stata assegnata a una configurazione da valutare. Se non è attualmente presente nessuna assegnazione, ottiene l'assegnazione e prepara la macchina virtuale come segue:
  - Autenticazione della macchina virtuale usando un'[identità gestita](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installazione della versione più recente dell'estensione **Microsoft.GuestConfiguration**
  - Installazione degli [strumenti di convalida](#validation-tools) e delle dipendenze, se necessario

Se il **DeployIfNotExists** assegnazione è Non conforme, un [attività di monitoraggio e aggiornamento](../how-to/remediate-resources.md#create-a-remediation-task) può essere utilizzato.

Una volta il **DeployIfNotExists** assegnazione è conforme, il **Audit** assegnazione di criteri Usa gli strumenti di convalida locale per determinare se l'assegnazione di configurazione è conforme o Non conformi.
Lo strumento di convalida fornisce i risultati al client della configurazione guest. Il client inoltra i risultati all'estensione guest, che li rende disponibili tramite il provider di risorse di configurazione guest.

Criteri di Azure usa la proprietà **complianceStatus** dei provider di risorse della configurazione guest per segnalare la conformità nel nodo **Conformità**. Per altre informazioni, vedere [Ottenere dati sulla conformità](../how-to/getting-compliance-data.md).

> [!NOTE]
> Per ogni definizione di configurazione guest devono esistere entrambe le definizioni dei criteri **DeployIfNotExists** e **Audit**.

Tutti i criteri predefiniti per la configurazione guest sono inclusi in un'iniziativa per raggruppare le definizioni da usare nelle assegnazioni. L'iniziativa predefinita denominata *[Anteprima]: Controllo delle impostazioni di sicurezza della password nelle macchine virtuali Linux e Windows* contiene 18 criteri. Esistono sei coppie **DeployIfNotExists** e **Audit** per Windows e tre coppie per Linux. In ogni caso, la logica all'interno della definizione convalida solo che il sistema operativo di destinazione venga valutato in base alla definizione di [regola dei criteri](definition-structure.md#policy-rule).

## <a name="client-log-files"></a>File di log client

L'estensione di configurazione Guest scrive i file di log nelle posizioni seguenti:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

In cui `<version>` si riferisce al numero di versione corrente.

## <a name="guest-configuration-samples"></a>Esempi di configurazione di guest

Esempi di configurazione dei criteri Guest sono disponibili nei percorsi seguenti:

- [Indice esempi - configurazione di Guest](../samples/index.md#guest-configuration)
- [Repository GitHub di esempi di criteri di Azure](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration).

## <a name="next-steps"></a>Passaggi successivi

- Esaminare gli esempi nella [esempi di criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](effects.md).
- Comprendere come [a livello di codice, creare criteri](../how-to/programmatically-create.md).
- Informazioni su come [ottenere i dati di conformità](../how-to/getting-compliance-data.md).
- Informazioni su come [monitora e aggiorna le risorse non conformi](../how-to/remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/index.md).