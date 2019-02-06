---
title: Informazioni su come eseguire controlli all'interno di una macchina virtuale
description: Informazioni su come Criteri di Azure usa la configurazione guest per controllare le impostazioni all'interno di una macchina virtuale di Azure.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/29/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 77d99c90e65647a1f4a4efb07ff5520596fa54cf
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295169"
---
# <a name="understand-azure-policys-guest-configuration"></a>Comprendere la configurazione guest di Criteri di Azure

Oltre al controllo e alla [correzione](../how-to/remediate-resources.md) delle risorse di Azure, Criteri di Azure è in grado di controllare le impostazioni all'interno di una macchina virtuale. La convalida viene eseguita dall'estensione della configurazione guest e dal client. L'estensione tramite il client convalida le impostazioni quali la configurazione del sistema operativo, la configurazione o la presenza dell'applicazione, le impostazioni di ambiente e altro ancora.

> [!IMPORTANT]
> Attualmente con la configurazione g sono supportati solo i criteri **incorporati**.

## <a name="extension-and-client"></a>Estensione e client

Per controllare le impostazioni all'interno di una macchina virtuale, è abilitata un'[estensione macchina virtuale](../../../virtual-machines/extensions/overview.md). L'estensione scarica l'assegnazione dei criteri applicabile e la configurazione corrispondente.

### <a name="register-guest-configuration-resource-provider"></a>Registrare il provider di risorse della configurazione guest

Prima di poter usare la configurazione guest, è necessario registrare il provider di risorse. È possibile eseguire la registrazione nel portale o con PowerShell.

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
| Windows|[Microsoft DSC (Desired State Configuration)](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby e Python vengono installati dall'estensione della configurazione guest. |

### <a name="validation-frequency"></a>Frequenza di convalida

Il client della configurazione guest verifica la presenza di nuovi contenuti ogni cinque minuti.
Dopo aver ricevuto un'assegnazione guest, le impostazioni vengono controllate a intervalli di 15 minuti.
Al termine del controllo, i risultati vengono inviati al provider di risorse di configurazione guest.
Quando vengono applicati criteri di tipo [trigger di valutazione](../how-to/get-compliance-data.md#evaluation-triggers), nel provider di risorse di configurazione guest viene scritto lo stato del computer.
In questo modo, Criteri di Azure può valutare le proprietà di Azure Resource Manager.
Una valutazione on demand di Criteri recupera il valore più recente dal provider di risorse di configurazione guest,
ma non attiva un nuovo controllo della configurazione nella macchina virtuale.

### <a name="supported-client-types"></a>Tipi di client supportati

La tabella seguente elenca i sistemi operativi supportati su Immagini di Azure:

|Editore|NOME|Versioni|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux.|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> La configurazione guest non è attualmente supportata nelle immagini di macchina virtuale personalizzate.

### <a name="unsupported-client-types"></a>Tipi di client non supportati

Nella tabella seguente sono elencati i sistemi operativi non supportati:

|Sistema operativo|Note|
|-|-|
|Client Windows | I sistemi operativi client, ad esempio Windows 7 e Windows 10, non sono supportati.
|Windows Server 2016 Nano Server | Non supportati.|

## <a name="guest-configuration-definition-requirements"></a>Requisiti per la definizione della configurazione guest

Ogni controllo eseguito dalla configurazione guest richiede due definizioni di criteri, **DeployIfNotExists** e **Audit**. **DeployIfNotExists** viene usato per preparare la macchina virtuale con l'agente di configurazione guest e altri componenti per supportare gli [strumenti di convalida](#validation-tools).

La definizione dei criteri **DeployIfNotExists** convalida e corregge gli elementi seguenti:

- Conferma che la macchina virtuale sia stata assegnata a una configurazione da valutare. Se non è attualmente presente nessuna assegnazione, ottiene l'assegnazione e prepara la macchina virtuale come segue:
  - Autenticazione della macchina virtuale usando un'[identità gestita](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installazione della versione più recente dell'estensione **Microsoft.GuestConfiguration**
  - Installazione degli [strumenti di convalida](#validation-tools) e delle dipendenze, se necessario

Una volta che **DeployIfNotExists** è conforme, la definizione dei criteri **Audit** usa gli strumenti di convalida locali per determinare se l'assegnazione della configurazione assegnata è conforme o non conforme. Lo strumento di convalida fornisce i risultati al client della configurazione guest. Il client inoltra i risultati all'estensione guest, che li rende disponibili tramite il provider di risorse di configurazione guest.

Criteri di Azure usa la proprietà **complianceStatus** dei provider di risorse della configurazione guest per segnalare la conformità nel nodo **Conformità**. Per altre informazioni, vedere [Ottenere dati sulla conformità](../how-to/getting-compliance-data.md).

> [!NOTE]
> Per ogni definizione di configurazione guest devono esistere entrambe le definizioni dei criteri **DeployIfNotExists** e **Audit**.

Tutti i criteri predefiniti per la configurazione guest sono inclusi in un'iniziativa per raggruppare le definizioni da usare nelle assegnazioni. L'iniziativa predefinita denominata *[Anteprima]: Controllo delle impostazioni di sicurezza della password nelle macchine virtuali Linux e Windows* contiene 18 criteri. Esistono sei coppie **DeployIfNotExists** e **Audit** per Windows e tre coppie per Linux. In ogni caso, la logica all'interno della definizione convalida solo che il sistema operativo di destinazione venga valutato in base alla definizione di [regola dei criteri](definition-structure.md#policy-rule).

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md)
- Vedere la [struttura delle definizioni dei criteri](definition-structure.md)
- Vedere [Informazioni sugli effetti di Criteri](effects.md)
- Informazioni su come [creare criteri a livello di programmazione](../how-to/programmatically-create.md)
- Informazioni su come [ottenere dati sulla conformità](../how-to/getting-compliance-data.md)
- Informazioni su come [correggere le risorse non conformi](../how-to/remediate-resources.md)
- Scoprire le caratteristiche di un gruppo di gestione con [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/index.md)
