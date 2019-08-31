---
title: Informazioni su come controllare il contenuto di un computer
description: Informazioni su come i criteri di Azure usano la configurazione Guest per controllare le impostazioni all'interno di una macchina di Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 269d13e0b64d2da7a21316b16c88acde46ba1722
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194631"
---
# <a name="understand-azure-policys-guest-configuration"></a>Comprendere la configurazione guest di Criteri di Azure

Oltre a controllare e [correggere](../how-to/remediate-resources.md) le risorse di Azure, i criteri di Azure possono controllare le impostazioni all'interno di un computer. La convalida viene eseguita dall'estensione della configurazione guest e dal client. L'estensione tramite il client convalida le impostazioni quali la configurazione del sistema operativo, la configurazione o la presenza dell'applicazione, le impostazioni di ambiente e altro ancora.

A questo punto, la configurazione Guest di criteri di Azure consente di eseguire solo un controllo delle impostazioni nel computer.
Non è ancora possibile applicare le configurazioni.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>Estensione e client

Per controllare le impostazioni all'interno di un computer, è abilitata un' [estensione della macchina virtuale](../../../virtual-machines/extensions/overview.md) . L'estensione scarica l'assegnazione dei criteri applicabile e la configurazione corrispondente.

### <a name="limits-set-on-the-exension"></a>Limiti impostati per exension

Per limitare l'estensione dall'effetto sulle applicazioni in esecuzione all'interno del computer, la configurazione Guest non può superare il 5% di utilizzo della CPU.
Questo è il vero boh per le configurazioni fornite da Microsoft come "predefinite" e per le configurazioni personalizzate create dai clienti.

## <a name="register-guest-configuration-resource-provider"></a>Registrare il provider di risorse della configurazione guest

Prima di poter usare la configurazione guest, è necessario registrare il provider di risorse. È possibile eseguire la registrazione nel portale o con PowerShell. Il provider di risorse viene registrato automaticamente se l'assegnazione dei criteri di configurazione Guest viene eseguita tramite il portale.

### <a name="registration---portal"></a>Registrazione - Portale

Per registrare il provider di risorse per la configurazione guest tramite il portale di Azure, seguire questa procedura:

1. Avviare il portale di Azure e fare clic su **Tutti i servizi**. Cercare e selezionare **Sottoscrizioni**.

1. Trovare e fare clic sulla sottoscrizione per cui si intende abilitare la configurazione guest.

1. Nel menu a sinistra della pagina **Sottoscrizione** fare clic su **Provider di risorse**.

1. Per filtrare o scorrere fino a individuare **Microsoft.GuestConfiguration**, quindi fare clic su **Registra** sulla stessa riga.

### <a name="registration---powershell"></a>Registrazione - PowerShell

Per registrare il provider di risorse per la configurazione guest tramite PowerShell, eseguire il seguente comando:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Strumenti di convalida

All'interno del computer, il client di configurazione Guest utilizza gli strumenti locali per eseguire il controllo.

La tabella seguente elenca gli strumenti locali usati on ciascun sistema operativo supportato:

|Sistema operativo|Strumento di convalida|Note|
|-|-|-|
|Windows|[Microsoft DSC (Desired State Configuration)](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby e Python vengono installati dall'estensione della configurazione guest. |

### <a name="validation-frequency"></a>Frequenza di convalida

Il client della configurazione guest verifica la presenza di nuovi contenuti ogni cinque minuti. Dopo aver ricevuto un'assegnazione guest, le impostazioni vengono controllate a intervalli di 15 minuti. Al termine del controllo, i risultati vengono inviati al provider di risorse di configurazione guest. Quando vengono applicati criteri di tipo [trigger di valutazione](../how-to/get-compliance-data.md#evaluation-triggers), nel provider di risorse di configurazione guest viene scritto lo stato del computer. In questo modo, Criteri di Azure può valutare le proprietà di Azure Resource Manager. Una valutazione di criteri di Azure su richiesta Recupera il valore più recente dal provider di risorse di configurazione Guest. Tuttavia, non attiva un nuovo controllo della configurazione all'interno del computer.

## <a name="supported-client-types"></a>Tipi di client supportati

La tabella seguente elenca i sistemi operativi supportati su Immagini di Azure:

|Pubblicato da|Name|Versioni|
|-|-|-|
|Canonico|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 datacenter, 2019 Datacenter|
|Microsoft|Client Windows|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux.|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> La configurazione Guest può controllare i nodi che eseguono un sistema operativo supportato. Se si desidera controllare le macchine virtuali che utilizzano un'immagine personalizzata, è necessario duplicare la definizione **DeployIfNotExists** e modificare la sezione **if** per includere le proprietà dell'immagine.

### <a name="unsupported-client-types"></a>Tipi di client non supportati

Windows Server nano server non è supportato in alcuna versione.

## <a name="guest-configuration-extension-network-requirements"></a>Requisiti di rete dell'estensione di configurazione Guest

Per comunicare con il provider di risorse di configurazione Guest in Azure, i computer richiedono l'accesso in uscita ai Data Center di Azure sulla porta **443**. Se si usa una rete virtuale privata in Azure e non si consente il traffico in uscita, è necessario configurare le eccezioni usando le regole del [gruppo di sicurezza di rete](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) . Al momento non esiste un tag di servizio per la configurazione Guest di criteri di Azure.

Per gli elenchi di indirizzi IP è possibile scaricare [Microsoft Azure intervalli IP del Data Center](https://www.microsoft.com/download/details.aspx?id=41653). Questo file viene aggiornato ogni settimana e presenta gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. È sufficiente consentire l'accesso in uscita agli indirizzi IP nelle aree in cui vengono distribuite le macchine virtuali.

> [!NOTE]
> Il file XML degli indirizzi IP dei data center di Azure elenca gli intervalli di indirizzi IP usati nei data center di Microsoft Azure. Il file include gli intervalli per le risorse di calcolo, SQL e di archiviazione. Viene pubblicata una versione aggiornata del file ogni settimana. Il file include gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. I nuovi intervalli riportati nel file non vengono usati nei data center per almeno una settimana. È consigliabile scaricare il nuovo file XML ogni settimana e aggiornare quindi il sito per identificare correttamente i servizi in esecuzione in Azure. Per gli utenti di ExpressRoute è importante sottolineare che questo file viene usato per aggiornare l'annuncio BGP (Border Gateway Protocol) dello spazio di Azure la prima settimana del mese.

## <a name="guest-configuration-definition-requirements"></a>Requisiti per la definizione della configurazione guest

Ogni controllo eseguito dalla configurazione Guest richiede due definizioni di criteri, una definizione **DeployIfNotExists** e una definizione **AuditIfNotExists** . La definizione **DeployIfNotExists** viene utilizzata per preparare il computer con l'agente di configurazione Guest e altri componenti per supportare gli [strumenti di convalida](#validation-tools).

La definizione dei criteri **DeployIfNotExists** convalida e corregge gli elementi seguenti:

- Verificare che al computer sia stata assegnata una configurazione da valutare. Se non è attualmente presente alcuna assegnazione, ottenere l'assegnazione e preparare il computer:
  - Autenticazione al computer tramite un' [identità gestita](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Installazione della versione più recente dell'estensione **Microsoft.GuestConfiguration**
  - Installazione degli [strumenti di convalida](#validation-tools) e delle dipendenze, se necessario

Se l'assegnazione **DeployIfNotExists** non è conforme, è possibile utilizzare un' [attività di correzione](../how-to/remediate-resources.md#create-a-remediation-task) .

Quando l'assegnazione **DeployIfNotExists** è conforme, l'assegnazione dei criteri **AuditIfNotExists** usa gli strumenti di convalida locali per determinare se l'assegnazione di configurazione è conforme o non conforme.
Lo strumento di convalida fornisce i risultati al client della configurazione guest. Il client inoltra i risultati all'estensione guest, che li rende disponibili tramite il provider di risorse di configurazione guest.

Criteri di Azure usa la proprietà **complianceStatus** dei provider di risorse della configurazione guest per segnalare la conformità nel nodo **Conformità**. Per altre informazioni, vedere [Ottenere dati sulla conformità](../how-to/getting-compliance-data.md).

> [!NOTE]
> Il criterio **DeployIfNotExists** è necessario affinché i criteri **AuditIfNotExists** restituiscano i risultati.
> Senza **DeployIfNotExists**, il criterio **AuditIfNotExists** Mostra le risorse "0 di 0" come stato.

Tutti i criteri predefiniti per la configurazione guest sono inclusi in un'iniziativa per raggruppare le definizioni da usare nelle assegnazioni. L'iniziativa predefinita denominata *[Anteprima]: Controllare le impostazioni di sicurezza delle password nei computer* Linux e Windows contiene 18 criteri. Esistono sei coppie **DeployIfNotExists** e **AuditIfNotExists** per Windows e tre coppie per Linux. In ogni caso, la logica all'interno della definizione convalida solo che il sistema operativo di destinazione venga valutato in base alla definizione di [regola dei criteri](definition-structure.md#policy-rule).

### <a name="multiple-assignments"></a>Più assegnazioni

I criteri di configurazione Guest attualmente supportano solo l'assegnazione della stessa assegnazione Guest una volta per ogni computer, anche se l'assegnazione dei criteri USA parametri diversi.

## <a name="built-in-resource-modules"></a>Moduli di risorse predefiniti

Quando si installa l'estensione di configurazione Guest, il modulo di PowerShell ' GuestConfiguration ' è incluso nella versione più recente dei moduli di risorse DSC. È possibile scaricare questo modulo dal PowerShell Gallery usando il collegamento "download manuale" della pagina del modulo [GuestConfiguration/](https://www.powershellgallery.com/packages/GuestConfiguration/).
Il formato del file ". nupkg" può essere rinominato in ". zip" per decomprimere ed esaminare.

## <a name="client-log-files"></a>File di log del client

L'estensione di configurazione Guest scrive i file di log nei percorsi seguenti:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Dove `<version>` si riferisce al numero di versione corrente.

## <a name="guest-configuration-samples"></a>Esempi di configurazione Guest

Gli esempi per la configurazione Guest per i criteri sono disponibili nei percorsi seguenti:

- [Indice degli esempi-configurazione Guest](../samples/index.md#guest-configuration)
- [Repository GitHub degli esempi di criteri di Azure](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration).

## <a name="next-steps"></a>Passaggi successivi

- Esaminare gli esempi in [esempi di criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](effects.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Informazioni su come [ottenere i dati di conformità](../how-to/getting-compliance-data.md).
- Informazioni su come monitorare e [aggiornare le risorse non](../how-to/remediate-resources.md)conformi.
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/index.md).
