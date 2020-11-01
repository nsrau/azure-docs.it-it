---
title: Risoluzione dei problemi comuni
description: Informazioni su come risolvere i problemi relativi alla creazione di definizioni di criteri, al vario SDK e al componente aggiuntivo per Kubernetes.
ms.date: 10/30/2020
ms.topic: troubleshooting
ms.openlocfilehash: 74b622dd41fb28e845a35780e5d06588189ec029
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146280"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Risolvere gli errori usando criteri di Azure

È possibile che si verifichino errori durante la creazione di definizioni di criteri, l'uso di SDK o la configurazione del componente aggiuntivo [criteri di Azure per Kubernetes](../concepts/policy-for-kubernetes.md) . Questo articolo descrive vari errori generali che possono verificarsi e come risolverli.

## <a name="finding-error-details"></a>Ricerca dei dettagli di errore

Il percorso dei dettagli dell'errore dipende dall'azione che ha causato l'errore.

- Quando si lavora con un criterio personalizzato, provare il portale di Azure per ottenere commenti e suggerimenti sullo schema o esaminare i dati di [conformità](../how-to/get-compliance-data.md) risultanti per vedere come sono state valutate le risorse.
- Quando si lavora con vari SDK, l'SDK fornisce informazioni dettagliate sul motivo per cui la funzione non è riuscita.
- Quando si utilizza il componente aggiuntivo per Kubernetes, iniziare con la [registrazione](../concepts/policy-for-kubernetes.md#logging) nel cluster.

## <a name="general-errors"></a>Errori generali

### <a name="scenario-alias-not-found"></a>Scenario: alias non trovato

#### <a name="issue"></a>Problema

Criteri di Azure usa [alias](../concepts/definition-structure.md#aliases) per eseguire il mapping alle proprietà Azure Resource Manager.

#### <a name="cause"></a>Causa

Un alias errato o inesistente viene utilizzato in una definizione di criteri.

#### <a name="resolution"></a>Risoluzione

Prima di tutto, verificare che la proprietà Gestione risorse disponga di un alias. Usare l' [estensione di criteri di Azure per Visual Studio Code](../how-to/extension-for-vscode.md), [Azure Resource Graph](../../resource-graph/samples/starter.md#distinct-alias-values)o SDK per cercare gli alias disponibili. Se l'alias di una proprietà Gestione risorse non esiste, creare un ticket di supporto.

### <a name="scenario-evaluation-details-not-up-to-date"></a>Scenario: dettagli della valutazione non aggiornati

#### <a name="issue"></a>Problema

Una risorsa è nello stato "non avviato" o i dettagli di conformità non sono aggiornati.

#### <a name="cause"></a>Causa

Un nuovo criterio o un'assegnazione di iniziativa richiede circa 30 minuti per essere applicati. Le risorse nuove o aggiornate nell'ambito di un'assegnazione esistente diventano disponibili circa 15 minuti dopo. Un'analisi di conformità standard viene eseguita ogni 24 ore. Per altre informazioni, vedere [trigger di valutazione](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Risoluzione

Attendere prima di tutto la quantità di tempo appropriata per il completamento di una valutazione e la disponibilità dei risultati di conformità in portale di Azure o SDK. Per avviare una nuova analisi di valutazione con Azure PowerShell o l'API REST, vedere [analisi di valutazione su richiesta](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-compliance-not-as-expected"></a>Scenario: conformità non come previsto

#### <a name="issue"></a>Problema

Una risorsa non è nello stato di valutazione, _conforme_ o _non conforme_ , prevista per tale risorsa.

#### <a name="cause"></a>Causa

La risorsa non è nell'ambito corretto per l'assegnazione dei criteri o la definizione dei criteri non funziona come previsto.

#### <a name="resolution"></a>Risoluzione

Attenersi alla seguente procedura per risolvere i problemi della definizione dei criteri:

1. Attendere prima di tutto la quantità di tempo appropriata per il completamento di una valutazione e la disponibilità dei risultati di conformità in portale di Azure o SDK. Per avviare una nuova analisi di valutazione con Azure PowerShell o l'API REST, vedere [analisi di valutazione su richiesta](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Verificare che i parametri di assegnazione e l'ambito di assegnazione siano impostati correttamente.
1. Controllare la [modalità di definizione dei criteri](../concepts/definition-structure.md#mode):
   - Modalità' all'per tutti i tipi di risorsa.
   - Modalità' indexed ' se la definizione dei criteri controlla i tag o il percorso.
1. Verificare che l'ambito della risorsa non sia [escluso](../concepts/assignment-structure.md#excluded-scopes) o [esentato](../concepts/exemption-structure.md).
1. Se la conformità per un'assegnazione di criteri Mostra `0/0` risorse, non è stata determinata alcuna risorsa da applicare nell'ambito dell'assegnazione. Controllare sia la definizione dei criteri che l'ambito di assegnazione.
1. Per una risorsa non conforme che dovrebbe essere conforme, controllare la [determinazione dei motivi della mancata conformità](../how-to/determine-non-compliance.md). Il confronto tra la definizione e il valore della proprietà valutato indica il motivo per cui una risorsa non è conforme.
   - Se il **valore di destinazione** è errato, rivedere la definizione dei criteri.
   - Se il **valore corrente** è errato, convalidare il payload della risorsa tramite `resources.azure.com` .
1. Verificare la [risoluzione dei problemi: imposizione non come previsto](#scenario-enforcement-not-as-expected) per altri problemi comuni e soluzioni.

Se si riscontra ancora un problema con la definizione di criteri predefinita duplicata e personalizzata, creare un ticket di supporto in creazione di **un criterio** per instradare correttamente il problema.

### <a name="scenario-enforcement-not-as-expected"></a>Scenario: imposizione non come previsto

#### <a name="issue"></a>Problema

Una risorsa che si prevede venga applicata da criteri di Azure non è e non è presente alcuna voce nel [log attività di Azure](../../../azure-monitor/platform/platform-logs-overview.md).

#### <a name="cause"></a>Causa

L'assegnazione dei criteri è stata configurata per [enforcementMode](../concepts/assignment-structure.md#enforcement-mode) di _disabilitato_ . Mentre la modalità di imposizione è disabilitata, l'effetto del criterio non viene applicato e non è presente alcuna voce nel log attività.

#### <a name="resolution"></a>Risoluzione

Attenersi alla seguente procedura per risolvere i problemi relativi all'imposizione dell'assegnazione di criteri:

1. Attendere prima di tutto la quantità di tempo appropriata per il completamento di una valutazione e la disponibilità dei risultati di conformità in portale di Azure o SDK. Per avviare una nuova analisi di valutazione con Azure PowerShell o l'API REST, vedere [analisi di valutazione su richiesta](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Verificare che i parametri di assegnazione e l'ambito di assegnazione siano impostati correttamente e che **enforcementMode** sia _abilitato_ . 
1. Controllare la [modalità di definizione dei criteri](../concepts/definition-structure.md#mode):
   - Modalità' all'per tutti i tipi di risorsa.
   - Modalità' indexed ' se la definizione dei criteri controlla i tag o il percorso.
1. Verificare che l'ambito della risorsa non sia [escluso](../concepts/assignment-structure.md#excluded-scopes) o [esentato](../concepts/exemption-structure.md).
1. Verificare che il payload della risorsa corrisponda alla logica dei criteri. Questa operazione può essere eseguita [acquisendo una traccia har](../../../azure-portal/capture-browser-trace.md) o esaminando le proprietà del modello ARM.
1. Verificare i [problemi: conformità non come previsto](#scenario-compliance-not-as-expected) per altri problemi comuni e soluzioni.

Se si riscontra ancora un problema con la definizione di criteri predefinita duplicata e personalizzata, creare un ticket di supporto in creazione di **un criterio** per instradare correttamente il problema.

### <a name="scenario-denied-by-azure-policy"></a>Scenario: negato da criteri di Azure

#### <a name="issue"></a>Problema

La creazione o l'aggiornamento di una risorsa è stato negato.

#### <a name="cause"></a>Causa

Un'assegnazione di criteri all'ambito in cui si trova la risorsa nuova o aggiornata soddisfa i criteri di una definizione di criteri con un effetto di [negazione](../concepts/effects.md#deny) . Le riunioni di risorse non possono essere create o aggiornate per queste definizioni.

#### <a name="resolution"></a>Risoluzione

Il messaggio di errore di un'assegnazione di criteri Deny include gli ID di definizione dei criteri e di assegnazione dei criteri. Se le informazioni sull'errore nel messaggio vengono perse, sono disponibili anche nel [log attività](../../../azure-monitor/platform/activity-log.md#view-the-activity-log). Usare queste informazioni per ottenere altri dettagli per comprendere le restrizioni delle risorse e modificare le proprietà delle risorse nella richiesta in modo che corrispondano ai valori consentiti.

## <a name="template-errors"></a>Errori del modello

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scenario: funzioni supportate dal criterio elaborate dal modello

#### <a name="issue"></a>Problema

Criteri di Azure supporta varie funzioni e funzioni di modello di Azure Resource Manager (modello ARM) disponibili solo in una definizione di criteri. Gestione risorse elabora queste funzioni come parte di una distribuzione anziché come parte di una definizione dei criteri.

#### <a name="cause"></a>Causa

L'uso di funzioni supportate, ad esempio `parameter()` o `resourceGroup()` , comporta il risultato elaborato della funzione in fase di distribuzione anziché lasciare che la funzione per la definizione dei criteri e il motore di criteri di Azure venga elaborata.

#### <a name="resolution"></a>Risoluzione

Per passare una funzione in modo da far parte di una definizione dei criteri, eseguire l'escape dell'intera stringa con `[` tale che la proprietà ha un aspetto simile a `[[resourceGroup().tags.myTag]` . Il carattere di escape fa in modo che Gestione risorse considerino il valore come stringa durante l'elaborazione del modello. Criteri di Azure posiziona quindi la funzione nella definizione dei criteri, in modo che sia dinamica come previsto. Per altre informazioni, vedere [sintassi ed espressioni nei modelli Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-for-kubernetes-installation-errors"></a>Componente aggiuntivo per errori di installazione di Kubernetes

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>Scenario: l'installazione con il grafico Helm non riesce con la password

#### <a name="issue"></a>Problema

Il `helm install azure-policy-addon` comando ha esito negativo con uno dei messaggi seguenti:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Causa

La password generata include una virgola ( `,` ) su cui si divide il grafico Helm.

#### <a name="resolution"></a>Risoluzione

Eseguire l'escape della virgola ( `,` ) nel valore della password durante l'esecuzione `helm install azure-policy-addon` con una barra rovesciata ( `\` ).

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>Scenario: l'installazione con il grafico Helm non riesce perché il nome esiste già

#### <a name="issue"></a>Problema

Il `helm install azure-policy-addon` comando ha esito negativo con il messaggio seguente:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Causa

Il grafico Helm con il nome `azure-policy-addon` è già stato installato o parzialmente installato.

#### <a name="resolution"></a>Risoluzione

Seguire le istruzioni per [rimuovere il componente aggiuntivo criteri di Azure per Kubernetes](../concepts/policy-for-kubernetes.md#remove-the-add-on), quindi eseguire di nuovo il `helm install azure-policy-addon` comando.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Scenario: le identità assegnate dall'utente di macchine virtuali di Azure vengono sostituite dalle identità gestite assegnate dal sistema

#### <a name="issue"></a>Problema

Dopo aver assegnato le iniziative dei criteri di configurazione Guest alle impostazioni di controllo all'interno dei computer, le identità gestite assegnate dall'utente assegnate al computer non vengono più assegnate. Viene assegnata solo un'identità gestita assegnata dal sistema.

#### <a name="cause"></a>Causa

Le definizioni dei criteri usate in precedenza nelle definizioni DeployIfNotExists di configurazione Guest hanno garantito che un'identità assegnata dal sistema venga assegnata al computer, ma anche le assegnazioni di identità assegnate dall'utente.

#### <a name="resolution"></a>Risoluzione

Le definizioni che in precedenza hanno causato il problema vengono visualizzate come \[ deprecate \] e vengono sostituite dalle definizioni dei criteri che gestiscono i prerequisiti senza rimuovere l'identità gestita assegnata dall'utente. È necessario eseguire un passaggio manuale. Eliminare le assegnazioni di criteri esistenti contrassegnate \[ come deprecate \] e sostituirle con l'iniziativa dei criteri e le definizioni dei criteri dei prerequisiti aggiornati con lo stesso nome dell'originale.

Per una descrizione dettagliata, vedere il post di Blog seguente:

[Modifica importante rilasciata per i criteri di controllo della configurazione Guest](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

## <a name="add-on-for-kubernetes-general-errors"></a>Errori generali del componente aggiuntivo per Kubernetes

### <a name="scenario-add-on-doesnt-work-with-aks-clusters-on-version-119-preview"></a>Scenario: il componente aggiuntivo non funziona con i cluster AKS nella versione 1,19 (anteprima)

#### <a name="issue"></a>Problema

I cluster della versione 1,19 restituiscono questo errore tramite il controller gatekeeper e i pod dei webhook dei criteri:

```
2020/09/22 20:06:55 http: TLS handshake error from 10.244.1.14:44282: remote error: tls: bad certificate
```

#### <a name="cause"></a>Causa

I CLUSER di AKS nella versione 1,19 (anteprima) non sono ancora compatibili con il componente aggiuntivo criteri di Azure.

#### <a name="resolution"></a>Risoluzione

Evitare di usare Kubernetes 1,19 (anteprima) con il componente aggiuntivo criteri di Azure. Il componente aggiuntivo può essere utilizzato con qualsiasi versione disponibile a livello generale, ad esempio 1,16, 1,17 o 1,18.

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-egress-restrictions"></a>Scenario: il componente aggiuntivo non riesce a raggiungere l'endpoint del servizio criteri di Azure a causa di restrizioni in uscita

#### <a name="issue"></a>Problema

Il componente aggiuntivo non è in grado di raggiungere l'endpoint del servizio criteri di Azure e restituisce uno degli errori seguenti:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Causa

Questo problema si verifica quando un cluster in uscita è bloccato.

#### <a name="resolution"></a>Risoluzione

Verificare che i domini e le porte negli articoli seguenti siano aperti:

- [Regole di rete in uscita obbligatorie e FQDN per i cluster AKS](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Installare il componente aggiuntivo criteri di Azure per Azure Arc Enabled Kubernetes (anteprima)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-aad-pod-identity-configuration"></a>Scenario: il componente aggiuntivo non riesce a raggiungere l'endpoint del servizio criteri di Azure a causa della configurazione di AAD-Pod-Identity

#### <a name="issue"></a>Problema

Il componente aggiuntivo non è in grado di raggiungere l'endpoint del servizio criteri di Azure e restituisce uno degli errori seguenti:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Causa

Questo errore si verifica quando si installa _Add-Pod-Identity_ nel cluster e i pod di _sistema Kube_ non sono esclusi in _AAD-Pod-Identity_ .

I pod dell'identità gestita del nodo del componente _AAD-Pod-Identity_ modificano i nodi iptables per intercettare le chiamate all'endpoint dei metadati dell'istanza di Azure. Questa configurazione indica che qualsiasi richiesta effettuata all'endpoint dei metadati viene intercettata da NMI anche se il Pod non usa _AAD-Pod-Identity_ .
**AzurePodIdentityException** È possibile configurare la gestione dei certificati per informare _AAD-Pod-Identity_ che qualsiasi richiesta all'endpoint di metadati originata da un pod che corrisponde alle etichette definite in CRD deve essere inoltrata tramite proxy senza alcuna elaborazione in NMI.

#### <a name="resolution"></a>Risoluzione

Escludere i pod di sistema con `kubernetes.azure.com/managedby: aks` etichetta nello spazio dei nomi _Kube-System_ in _AAD-Pod-Identity_ configurando la **AzurePodIdentityException** CRD.

Per altre informazioni, vedere [disabilitare l'identità di AAD pod per un pod/applicazione specifico](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).

Per configurare un'eccezione, vedere questo esempio:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>Scenario: il provider di risorse non è registrato

#### <a name="issue"></a>Problema

Il componente aggiuntivo può raggiungere l'endpoint del servizio criteri di Azure, ma viene visualizzato l'errore seguente:

```
The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See https://aka.ms/policy-register-subscription for how to register subscriptions.
```

#### <a name="cause"></a>Causa

Il `Microsoft.PolicyInsights` provider di risorse non è registrato e deve essere registrato per il componente aggiuntivo per ottenere le definizioni dei criteri e restituire i dati di conformità.

#### <a name="resolution"></a>Risoluzione

Registrare il provider di risorse `Microsoft.PolicyInsights`. Per istruzioni, vedere [registrare un provider di risorse](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

### <a name="scenario-the-subscript-is-disabled"></a>Scenario: il pedice è disabilitato

#### <a name="issue"></a>Problema

Il componente aggiuntivo può raggiungere l'endpoint del servizio criteri di Azure, ma viene visualizzato l'errore seguente:

```
The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.
```

#### <a name="cause"></a>Causa

Questo errore indica che la sottoscrizione è stata determinata come problematica e che `Microsoft.PolicyInsights/DataPlaneBlocked` è stato aggiunto il flag funzionalità per bloccare la sottoscrizione.

#### <a name="resolution"></a>Risoluzione

Contattare il team delle funzionalità `azuredg@microsoft.com` per esaminare e risolvere il problema. 

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

- Ottieni risposte dagli esperti tramite [Microsoft Q&A](/answers/topics/azure-policy.html).
- Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
- Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto** .
