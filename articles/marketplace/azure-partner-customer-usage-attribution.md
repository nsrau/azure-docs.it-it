---
title: Partner del Marketplace commerciale e attribuzione dell'utilizzo dei clienti
description: Panoramica su come tenere traccia dell'utilizzo da parte dei clienti per le soluzioni di Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 11/4/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: e9e630f4199e0bfb67509f008f403c0a7a490887
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436692"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>Partner del Marketplace commerciale e attribuzione dell'utilizzo dei clienti

L'attribuzione dell'utilizzo da parte dei clienti è un metodo per associare le risorse di Azure in esecuzione nelle sottoscrizioni dei clienti, distribuite per eseguire la soluzione, con l'utente come partner. La creazione di queste associazioni nei sistemi Microsoft interni offre maggiore visibilità sul footprint di Azure che esegue il proprio software. Adottando questa funzionalità di monitoraggio, è possibile allinearsi con i team di vendita Microsoft e ottenere credito per i programmi per partner Microsoft.

È possibile creare l'associazione tramite Azure Marketplace, il repository di avvio rapido, i repository GitHub privati e il coinvolgimento dei clienti in una relazione 1 a 1 che crea un IP durevole (ad esempio nel caso dello sviluppo di un'app).

L'attribuzione dell'utilizzo da parte dei clienti supporta tre opzioni di distribuzione:

- Modelli di Azure Resource Manager: i partner possono usare i modelli di Resource Manager per distribuire i servizi di Azure per l'esecuzione del software del partner. I partner possono creare i modelli di Resource Manager che definiscono l'infrastruttura e la configurazione della soluzione Azure. Un modello di Resource Manager consente a partner e rispettivi clienti di distribuire la propria soluzione per tutto il suo ciclo di vita. È possibile essere certi che le risorse vengano distribuite in uno stato coerente.
- API di Azure Resource Manager: I partner possono chiamare le API di Gestione risorse direttamente per distribuire un modello di Gestione risorse o per generare le chiamate API per la fornitura diretta di servizi Azure.
- Terraform: i partner possono usare Terraform per distribuire un modello di Resource Manager o per distribuire direttamente i servizi di Azure.

>[!IMPORTANT]
>- L'attribuzione dell'utilizzo da parte dei clienti non è destinata al monitoraggio del lavoro degli integratori di sistemi, dei provider di servizi gestiti o degli strumenti progettati per distribuire e gestire il software in esecuzione in Azure.
>
>- L'attribuzione dell'utilizzo da parte dei clienti riguarda le nuove distribuzioni e NON supporta l'assegnazione di tag alle risorse esistenti già distribuite.
>
>- L'attribuzione dell'utilizzo da parte dei clienti è necessaria per le offerte di [applicazioni di Azure](./create-new-azure-apps-offer.md) pubblicate in Azure Marketplace.
>
>- Non tutti i servizi di Azure sono compatibili con l'attribuzione dell'utilizzo del cliente. I servizi di Azure Kubernetes (AKS) e i set di scalabilità di macchine virtuali presentano attualmente problemi noti che provocano la segnalazione dell'utilizzo.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>Crea GUID

Un GUID è un identificatore di riferimento univoco con 32 cifre esadecimali. Per creare GUID per il rilevamento, è necessario usare un generatore di GUID, ad esempio tramite PowerShell.

```powershell
[guid]::NewGuid()
```

Si consiglia di creare un GUID univoco per ogni canale di offerta e distribuzione per ogni prodotto. È possibile scegliere di usare un unico GUID per i vari canali di distribuzione del prodotto se non si vuole suddividere la creazione di report.

Se si distribuisce un prodotto usando un modello ed è disponibile sia in Azure Marketplace che in GitHub, è possibile creare e registrare due GUID distinti:

- Prodotto A su Azure Marketplace
- Prodotto A su GitHub

La creazione di report viene eseguita tramite GUID e ID Microsoft Partner Network.

È anche possibile tenere traccia dell'utilizzo a un livello più granulare registrando i GUID aggiuntivi e modificando i GUID tra i piani, dove i piani sono varianti di un'offerta.

## <a name="register-guids"></a>Registrare i GUID

I GUID devono essere registrati nel Centro per i partner per consentire l'attribuzione dell'utilizzo da parte dei clienti.

Dopo avere aggiunto il GUID al modello o nell'agente utente e avere registrato il GUID nel Centro per i partner, vengono monitorate le distribuzioni future.

> [!NOTE]
> Se si pubblica l'offerta [applicazione Azure](./create-new-azure-apps-offer.md) in Azure Marketplace tramite il centro per i partner, qualsiasi nuovo GUID usato nel modello verrà automaticamente registrato nel profilo del centro per i partner quando il modello viene caricato.  

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard).

1. Eseguire la registrazione come [editore del marketplace commerciale](https://aka.ms/JoinMarketplace).

   * I partner devono [avere un profilo nel Centro per i partner](./partner-center-portal/create-account.md). Si consiglia di inserire l'offerta in Azure Marketplace o AppSource.
   * I partner possono registrare più GUID.
   * I partner possono registrare GUID per modelli e offerte di soluzioni non del marketplace.

1. Selezionare **Impostazioni** (icona a forma di ingranaggio) nell'angolo superiore destro > **Impostazioni account**.

1. In **Organization profile**  >  **identificatori** del profilo organizzazione selezionare **Aggiungi GUID di rilevamento**.

1. Nella casella **GUID** immettere il GUID di verifica. Immettere solo il GUID senza il `pid-` prefisso. Nella casella **Descrizione** immettere il nome o la descrizione dell'offerta.

1. Per registrare più GUID, selezionare di nuovo **Add Tracking GUID** (Aggiungi GUID di rilevamento). Nella pagina verranno visualizzate finestre aggiuntive.

1. Selezionare **Salva**.

## <a name="use-resource-manager-templates"></a>Usare i modelli di Resource Manager
Molte soluzioni dei partner vengono distribuite usando i modelli di Azure Resource Manager. Se si dispone di un modello di Gestione risorse disponibile in Azure Marketplace, su GitHub o come guida introduttiva, il processo di modifica del modello per consentire l'attribuzione dell'utilizzo dei clienti è semplice.

> [!NOTE]
> Per altre informazioni sulla creazione e sulla pubblicazione di modelli di soluzione, vedere
> * [Creare e distribuire il primo modello di Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
>* [Offerta di applicazione Azure](./create-new-azure-apps-offer.md).
>* Video: [creazione di modelli di soluzioni e applicazioni gestite per Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


Per aggiungere un identificatore univoco globale (GUID), si apporta una sola modifica nel file di modello principale:

1. [Creare un GUID](#create-guids) usando il metodo consigliato e [registrare il GUID](#register-guids).

1. Aprire il modello di Resource Manager.

1. Aggiungere una nuova risorsa di tipo [Microsoft. resources/Deployments](/azure/templates/microsoft.resources/deployments) nel file di modello principale. La risorsa deve essere solo nel file **mainTemplate.json** o **azuredeploy.json**, non in un template annidato o collegato.

1. Immettere il valore GUID dopo il `pid-` prefisso come nome della risorsa. Se ad esempio il GUID è eb7927c8-dd66-43e1-b0cf-c346a422063, il nome della risorsa sarà _PID-eb7927c8-dd66-43e1-b0cf-c346a422063_.

1. Controllare il modello per individuare eventuali errori.

1. Pubblicare nuovamente il modello nel repository appropriato.

1. [Verificare l'esito positivo del GUID nella distribuzione del modello](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Esempio di codice di modello di Resource Manager

Per abilitare il rilevamento delle risorse per il modello, è necessario aggiungere la risorsa aggiuntiva seguente nella sezione risorse. Assicurarsi di modificare il seguente codice di esempio con il proprio input quando aggiunto al file del modello principale.
La risorsa deve essere aggiunta solo nel file **mainTemplate.json** o **azuredeploy.json**, non in un template annidato o collegato.

```json
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```

## <a name="use-the-resource-manager-apis"></a>Utilizzare le API di Resource Manager

In alcuni casi, è preferibile effettuare chiamate direttamente alle API REST di Resource Manager per distribuire i servizi di Azure. [Azure supporta più SDK](../index.yml?pivot=sdkstools) per abilitare queste chiamate. È possibile usare uno degli SDK o chiamare le API REST direttamente per distribuire le risorse.

Se si usa un modello Resource Manager, è consigliabile assegnare tag alla soluzione seguendo le istruzioni descritte in precedenza. Se non si utilizza un modello di Resource Manager ed è possibile effettuare chiamate dirette alle API, è comunque possibile assegnare tag all'installazione remota per associare l'utilizzo delle risorse di Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Applicare tag a una distribuzione con le API di Resource Manager

Per abilitare l'attribuzione dell'utilizzo da parte dei clienti, quando si progettano le chiamate API, includere un GUID nell'intestazione agente utente nella richiesta. Aggiungere il GUID per ogni offerta o SKU. Formattare la stringa con il `pid-` prefisso e includere il GUID generato dal partner. Di seguito è riportato un esempio del formato GUID da inserire nell'agente utente:

![Esempio di formato GUID](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> Il formato della stringa è importante. Se il `pid-` prefisso non è incluso, non è possibile eseguire una query sui dati. Diversi SDK eseguono il rilevamento in modo diverso. Per implementare questo metodo è necessario rivedere il supporto e l'approccio di rilevamento per l'SDK Azure che si preferisce.

#### <a name="example-the-python-sdk"></a>Esempio: Python SDK

Per Python, usare l’attributo **config**. È possibile aggiungere l’attributo solo a un agente utente. Ad esempio:

![Aggiungere l'attributo a un agente utente](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
> Aggiungere l'attributo per ogni client. Non esiste una configurazione statica globale. È possibile aggiungere un tag a una client factory per essere sicuri che ogni client stia eseguendo il rilevamento. Per ulteriori informazioni, vedere questo [esempio di client factory in GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-the-net-sdk"></a>Esempio: .NET SDK

Per .NET, assicurarsi di impostare l'agente utente. La libreria [Microsoft. Azure. Management. Fluent](/dotnet/api/microsoft.azure.management.fluent) può essere usata per impostare l'agente utente con il codice seguente, ad esempio in C#:

```csharp

var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Aggiungere un tag a una distribuzione usando Azure PowerShell

Se si distribuiscono risorse tramite Azure PowerShell, aggiungere il GUID utilizzando il seguente metodo:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Aggiungere un tag a una distribuzione usando l’interfaccia della riga di comando di Azure

Quando si utilizza Azure CLI per aggiungere il proprio GUID, impostare la variabile di ambiente **AZURE_HTTP_USER_AGENT**. È possibile impostare questa variabile nell'ambito di uno script. È anche possibile impostare la variabile a livello globale per l'ambito della shell:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Per altre informazioni, vedere [Azure SDK per Go](/azure/developer/go/).

## <a name="use-terraform"></a>Usare Terraform

Il supporto per Terraform è disponibile tramite la versione 1.21.0 del provider di Azure: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Questo supporto si applica a tutti i partner che distribuiscono la propria soluzione tramite Terraform e a tutte le risorse distribuite e misurate dal provider di Azure (versione 1.21.0 o successiva).

Il provider di Azure per Terraform prevede un nuovo campo facoltativo denominato [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) che consente di specificare il GUID di verifica usato per la soluzione. Il valore di questo campo può anche essere ricavato dalla variabile di ambiente *ARM_PARTNER_ID*.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
I partner che desiderano monitorare la distribuzione tramite Terraform attraverso l'attribuzione dell'utilizzo da parte dei clienti devono procedere come segue:

* Creare un GUID (il GUID deve essere aggiunto per ogni offerta o SKU)
* Aggiornare il provider di Azure per impostare il valore di *partner_id* sul GUID (NON far precedere il GUID dal prefisso "pid-" ma impostare semplicemente il GUID effettivo)

## <a name="verify-the-guid-deployment"></a>Verificare la distribuzione GUID

Dopo aver modificato il modello ed eseguito un test di distribuzione, usare il seguente script di PowerShell per recuperare le risorse che sono state distribuite e a cui sono stati assegnati tag.

È possibile utilizzare lo script per verificare che il GUID sia stato aggiunto correttamente al modello di Resource Manager. Lo script non si applica alle distribuzioni tramite l'API di Resource Manager o Terraform.

Accedere ad Azure. Selezionare la sottoscrizione con la distribuzione che si desidera verificare prima di eseguire lo script. Eseguire lo script nel contesto della sottoscrizione della distribuzione.

Il **GUID** e il nome **resourceGroup** della distribuzione sono parametri necessari.

È possibile ottenere [lo script originale](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) su GitHub.

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>Report

È possibile trovare il report per l'attribuzione dell'utilizzo da parte dei clienti nel dashboard del Centro per i partner ([https://partner.microsoft.com/dashboard/partnerinsights/analytics/overview](https://partner.microsoft.com/dashboard/partnerinsights/analytics/overview)). Per visualizzare il report, è necessario usare le credenziali del Centro per i partner per accedere. Se si verificano problemi con il report o l'accesso, creare una [richiesta di supporto](#get-support).

## <a name="notify-your-customers"></a>Informare i clienti

I partner devono informare i propri clienti in merito alle distribuzioni che usano l'attribuzione dell'utilizzo da parte dei clienti. Microsoft segnala l'utilizzo di Azure associato a queste distribuzioni al partner. Gli esempi seguenti includono i contenuti che è possibile utilizzare per notificare ai clienti queste distribuzioni. Negli esempi sostituire \<PARTNER> con il nome della società. I partner devono garantire che la notifica sia allineata sia in linea con le politiche in materia di privacy e raccolta dei dati, comprese le opzioni di esclusione dei clienti dal rilevamento.

### <a name="notification-for-resource-manager-template-deployments"></a>Notifica per le distribuzioni dei modelli di Resource Manager

Quando si distribuisce questo modello, Microsoft è in grado di identificare l'installazione del \<PARTNER> software con le risorse di Azure che vengono distribuite. Microsoft è in grado di correlare le risorse di Azure usate per supportare il software. Microsoft raccoglie queste informazioni per fornire l'esperienza utente migliore con i propri prodotti e per il funzionamento delle proprie attività aziendali. I dati vengono raccolti e regolati dall'Informativa sulla privacy di Microsoft, disponibile all'indirizzo [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

### <a name="notification-for-sdk-or-api-deployments"></a>Notifica per le distribuzioni di API o SDK

Quando si distribuisce \<PARTNER> il software, Microsoft è in grado di identificare l'installazione del \<PARTNER> software con le risorse di Azure distribuite. Microsoft è in grado di correlare le risorse di Azure usate per supportare il software. Microsoft raccoglie queste informazioni per fornire l'esperienza utente migliore con i propri prodotti e per il funzionamento delle proprie attività aziendali. I dati vengono raccolti e regolati dall'Informativa sulla privacy di Microsoft, disponibile all'indirizzo [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="get-support"></a>Supporto

Per informazioni sulle opzioni di supporto disponibili nel Marketplace commerciale, vedere [supporto per il programma Commercial Marketplace nel centro per i partner](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>Come inviare una richiesta di consulenza tecnica

1. Visitare la pagina dei [servizi tecnici dei partner](https://aka.ms/TechnicalJourney).
1. Selezionare Infrastruttura cloud e gestione per visualizzare una nuova pagina con il percorso tecnico.
1. Nella sezione relativa ai servizi di distribuzione fare clic sul pulsante per l'invio di una richiesta
1. Accedere usando l'account del servizio gestito (account MPN) o AAD (account del dashboard del partner). In base alle credenziali di accesso, verrà visualizzato un modulo di richiesta online:
    * Completare/esaminare le informazioni di contatto.
    * I dettagli della consulenza possono essere già inseriti oppure è possibile selezionarli negli elenchi a discesa.
    * Immettere un titolo e la descrizione del problema (fornire il maggior numero possibile di dettagli).

1. Fare clic su Invia

Vedere le istruzioni dettagliate con i relativi screenshot in [Uso di servizi di prevendita e distribuzione tecnici](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>Passaggi successivi

Si verrà contattati da un consulente tecnico partner Microsoft per organizzare una chiamata in cui delineare le esigenze.

## <a name="faq"></a>Domande frequenti

**Qual è il vantaggio dell'aggiunta di GUID al modello?**

Microsoft fornisce ai partner una panoramica delle distribuzioni delle loro soluzioni da parte dei clienti, con informazioni dettagliate sull'utilizzo influenzato. Microsoft e il partner possono usare queste informazioni per aumentare l'engagement tra i team di vendita. Microsoft e il partner possono anche usare i dati per ottenere un'idea più coerente dell'impatto di un singolo partner sulla crescita di Azure.

**Dopo essere stato aggiunto, un GUID può essere modificato?**

Sì, un cliente o un partner di implementazione può personalizzare il modello e può modificare o rimuovere il GUID. Si consiglia ai partner di descrivere in modo proattivo il ruolo della risorsa e del GUID ai propri clienti e partner per evitare che il GUID venga rimosso o modificato. La modifica del GUID ha effetto solo su implementazioni e risorse nuove e non esistenti.

**È possibile monitorare i modelli distribuiti da un repository non Microsoft, ad esempio GitHub?**

Sì, se il GUID è presente quando il modello viene distribuito, viene rilevato il suo utilizzo. I partner devono comunque registrare i relativi GUID.

**Il cliente riceve anche la funzione di creazione report?**

I clienti sono in grado di rilevare l'utilizzo di singole risorse o gruppi di risorse definiti dall'utente all'interno del portale di Azure. I clienti non visualizzano l'utilizzo suddiviso in base al GUID.

**Questa metodologia di rilevamento è simile al DPOR (Digital Partner of Record)?**

Questo nuovo metodo per collegare la distribuzione e l'utilizzo alla soluzione di un partner fornisce un meccanismo per collegare una soluzione partner all'utilizzo di Azure. DPOR intende associare un partner di consulenza (integratore di sistemi) o di gestione (provider di servizio gestito) all'abbonamento Azure di un cliente.

**È possibile usare un disco rigido virtuale privato e personalizzato per un'offerta di modello di soluzione in Azure Marketplace?**

No, non è possibile. L'immagine della macchina virtuale deve provenire da Azure Marketplace, vedere: [Guida alla pubblicazione per le offerte di macchine virtuali in Azure Marketplace](marketplace-virtual-machines.md).

È possibile creare un'offerta di macchina virtuale nel marketplace usando il disco rigido virtuale personalizzato e contrassegnarla come privata, in modo che nessuno possa visualizzarla. Fare quindi riferimento a questa macchina virtuale nel modello di soluzione.

**Cosa significa che è impossibile aggiornare la proprietà *contentVersion* per il modello principale?**

Si tratta probabilmente di un bug, nei casi in cui il modello viene distribuito usando un TemplateLink da un altro modello che prevede un contentVersion precedente per qualche motivo. Una soluzione alternativa consiste nell'usare la proprietà metadata:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
