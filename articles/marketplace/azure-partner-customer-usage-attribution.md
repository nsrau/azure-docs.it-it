---
title: Attribuzione di utilizzo dei clienti e partner di Azure | Azure Marketplace
description: Panoramica su come tenere traccia dell'uso da parte dei clienti per le soluzioni di Azure Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.topic: article
ms.date: 11/17/2018
ms.author: yijenj
ms.openlocfilehash: dcab4d24ca948980f28483fd09f29588e0329b63
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800963"
---
# <a name="azure-partner-customer-usage-attribution"></a>Attribuzione dell'uso da parte dei clienti e dei partner di Azure

Le soluzioni software dei partner per Azure richiedono componenti di Azure o devono essere implementate direttamente sull'infrastruttura di Azure. I clienti che implementano la soluzione di un partner e forniscono le proprie risorse Azure possono avere difficoltà a ottenere visibilità sullo stato dell'implementazione e a ottenere informazioni dettagliate relative all'impatto sulla crescita di Azure. L'aggiunta di un livello di visibilità maggiore aiuta ad allinearsi con i team di vendita Microsoft e ottenere credito per i programmi per partner Microsoft.

Microsoft offre ora un metodo per aiutare i partner a monitorare meglio l'uso di Azure delle distribuzioni dei clienti del loro software su Azure. Questo nuovo metodo si basa sull'uso di Azure Resource Manager per orchestrare la distribuzione dei servizi di Azure.

I partner Microsoft possono associare l'utilizzo di Azure a qualsiasi risorsa Azure fornita per conto di un cliente. È possibile creare l'associazione tramite Azure Marketplace, il repository Quickstart, i repository privati GitHub e il coinvolgimento individuale dei clienti. Attribuzione di utilizzo dei clienti supporta tre opzioni di distribuzione:

- Modelli di Azure Resource Manager: I partner possono usare modelli di Resource Manager per distribuire i servizi di Azure per eseguire il software del partner. I partner possono creare i modelli di Resource Manager che definiscono l'infrastruttura e la configurazione della soluzione Azure. Un modello di Resource Manager consente a partner e rispettivi clienti di distribuire la propria soluzione per tutto il suo ciclo di vita. È possibile essere certi che le risorse vengano distribuite in uno stato coerente.
- API di Azure Resource Manager: I partner possono chiamare le API di Gestione risorse direttamente per distribuire un modello di Gestione risorse o per generare le chiamate API per la fornitura diretta di servizi Azure.
- Terraform: I partner possono utilizzare orchestrator cloud, ad esempio Terraform per distribuire un modello di Resource Manager o direttamente la distribuzione di servizi di Azure.

Attribuzione di utilizzo dei clienti è per la nuova distribuzione e non supporta l'assegnazione di tag alle risorse esistenti che sono già state distribuite.

Attribuzione di utilizzo dei clienti è necessario nei [l'applicazione Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer): offerta di modello di soluzione pubblicati in Azure Marketplace.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>Usare i modelli di Resource Manager
Molte soluzioni dei partner vengono distribuite nella sottoscrizione del cliente usando i modelli di Resource Manager. Se si dispone di un modello di Resource Manager che è disponibile in Azure Marketplace, in GitHub o come una Guida introduttiva, il processo per modificare il modello per abilitare l'attribuzione di utilizzo dei clienti dovrebbe essere molto semplice.

Per altre informazioni sulla creazione e sulla pubblicazione di modelli di soluzione, vedere

* [Creare e distribuire il primo modello di Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
* [Offerta di applicazione Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer).
* Video: [Building Solution Templates, and Managed Applications for the Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) (Creazione di modelli di soluzione e applicazioni gestite per Azure Marketplace).


## <a name="add-a-guid-to-your-template"></a>Aggiungere un GUID al modello

Per aggiungere un identificatore univoco globale (GUID), si apporta una sola modifica nel file di modello principale:

1. [Creare un GUID](#create-guids) usando il metodo consigliato e [registrare il GUID](#register-guids-and-offers).

1. Aprire il modello di Resource Manager.

1. Aggiungere una nuova risorsa nel file del modello principale. La risorsa deve essere solo nel file **mainTemplate.json** o **azuredeploy.json**, non in un template annidato o collegato.

1. Immettere il valore GUID che segue il prefisso **pid-** (ad esempio, pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Controllare il modello per individuare eventuali errori.

1. Pubblicare nuovamente il modello nel repository appropriato.

1. [Verificare l'esito positivo del GUID nella distribuzione del modello](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Esempio di codice di modello di Resource Manager

Per abilitare il rilevamento delle risorse per il modello, è necessario aggiungere la risorsa aggiuntiva seguente nella sezione risorse. Assicurarsi di modificare il seguente codice di esempio con il proprio input quando aggiunto al file del modello principale.
La risorsa deve essere aggiunta solo nel file **mainTemplate.json** o **azuredeploy.json**, non in un template annidato o collegato.

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
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

In alcuni casi, è preferibile effettuare chiamate direttamente alle API REST di Resource Manager per distribuire i servizi di Azure. [Azure supporta più SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) per abilitare queste chiamate. È possibile usare uno degli SDK o chiamare le API REST direttamente per distribuire le risorse.

Se si usa un modello Resource Manager, è consigliabile assegnare tag alla soluzione seguendo le istruzioni descritte in precedenza. Se non si utilizza un modello di Resource Manager ed è possibile effettuare chiamate dirette alle API, è comunque possibile assegnare tag all'installazione remota per associare l'utilizzo delle risorse di Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Applicare tag a una distribuzione con le API di Resource Manager

Per abilitare l'utilizzo del cliente attribuzione, quando si progettano le chiamate all'API, incluso un GUID nell'intestazione agente utente nella richiesta. Aggiungere il GUID per ogni offerta o SKU. Formattare la stringa con il prefisso **pid-** e includere il GUID generato dal partner. Di seguito è riportato un esempio del formato GUID da inserire nell'agente utente:

![Esempio di formato GUID](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> Il formato della stringa è importante. Se non è incluso il prefisso **pid-**, non è possibile eseguire query sui dati. Diversi SDK eseguono il rilevamento in modo diverso. Per implementare questo metodo è necessario rivedere il supporto e l'approccio di rilevamento per l'SDK Azure che si preferisce.

#### <a name="example-the-python-sdk"></a>Esempio: Python SDK

Per Python, usare l’attributo **config**. È possibile aggiungere l’attributo solo a un agente utente. Ad esempio:

![Aggiungere l'attributo a un agente utente](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Aggiungere l'attributo per ogni client. Non esiste una configurazione statica globale. È possibile aggiungere un tag a una client factory per essere sicuri che ogni client stia eseguendo il rilevamento. Per ulteriori informazioni, vedere questo [esempio di client factory in GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Aggiungere un tag a una distribuzione usando Azure PowerShell

Se si distribuiscono risorse tramite Azure PowerShell, aggiungere il GUID utilizzando il seguente metodo:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Aggiungere un tag a una distribuzione usando l’interfaccia della riga di comando di Azure

Quando si utilizza Azure CLI per aggiungere il proprio GUID, impostare la variabile di ambiente **AZURE_HTTP_USER_AGENT**. È possibile impostare questa variabile nell'ambito di uno script. È anche possibile impostare la variabile a livello globale per l'ambito della shell:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Per altre informazioni, vedere [Azure SDK per Go](https://docs.microsoft.com/go/azure/).

## <a name="use-terraform"></a>Usare Terraform

Il supporto per Terraform è disponibile tramite 1.21.0 del Provider di Azure versione: [ https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019 ](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Questo supporto si applica a tutti i partner che distribuiscono la soluzione tramite Terraform e tutte le risorse distribuite e conteggiate dal Provider di Azure (versione 1.21.0 o versione successiva).

Provider di Azure di Terraform aggiunto un nuovo campo facoltativo denominato [ *partner_id* ](https://www.terraform.io/docs/providers/azurerm/#partner_id) che è possibile specificare il GUID usato per la soluzione di verifica. Il valore di questo campo può anche essere derivato dal *ARM_PARTNER_ID* variabile di ambiente.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = “xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
I partner che vogliono ottenere la distribuzione tramite Terraform rilevati da attribuzione dell'utilizzo dei clienti devono eseguire le operazioni seguenti:

* Creare un GUID (GUID deve essere aggiunto per ogni offerta o dello SKU)
* Aggiornare il Provider di Azure per impostare il valore della *partner_id* al GUID (non si pre-correzione del GUID con "pid-", solo impostarlo sul GUID effettivo)

## <a name="create-guids"></a>Crea GUID

Un GUID è un numero di riferimento univoco con 32 cifre esadecimali. Per creare un GUID per il rilevamento, è necessario usare un generatore di GUID. Il team di Archiviazione di Azure ha creato un [modulo generatore di GUID](https://aka.ms/StoragePartners) che invierà un'e-mail con un GUID nel formato corretto e riutilizzabile nei vari sistemi di rilevamento.

> [!Note]
> È consigliabile usare [form del generatore GUID dell'archiviazione di Azure](https://aka.ms/StoragePartners) per creare il GUID. Per altre informazioni, vedere la sezione [Domande frequenti](#faq).

Si consiglia di creare un GUID univoco per ogni canale di offerta e distribuzione per ogni prodotto. È possibile scegliere di usare un unico GUID per i vari canali di distribuzione del prodotto se non si vuole suddividere la creazione di report.

Se si usa un modello per distribuire un prodotto disponibile su Azure Marketplace e su GitHub, è possibile creare e registrare 2 GUID distinti:

*   Prodotto A su Azure Marketplace
*   Prodotto A su GitHub

La creazione di report viene effettuata in base al valore partner (ID partner Microsoft) e ai GUID.

È anche possibile tracciare i GUID a un livello più granulare, come a livello di SKU (dove le SKU sono varianti di un'offerta).

## <a name="register-guids-and-offers"></a>Registrare GUID e offerte

Il GUID deve essere registrato per abilitare l'attribuzione di utilizzo dei clienti.

Tutte le registrazioni per i modelli GUID vengono eseguite tramite il portale Cloud Partner (CPP) di Azure Marketplace.

Dopo aver aggiunto il GUID per il modello o nell'agente utente e registrato il GUID nel CPP vengono tracciate tutte le distribuzioni.

1. Registrarsi ad [Azure Marketplace](https://aka.ms/listonazuremarketplace) e accedere al CPP.

   * Ai partner è richiesto di [avere un profilo in CPP](https://docs.microsoft.com/azure/marketplace/become-publisher). Si consiglia di inserire l'offerta in Azure Marketplace o AppSource.
   * I partner possono registrare più GUID.
   * I partner possono un GUID per i modelli e le offerte di soluzioni non di Marketplace.

1. Accedere al [Portale per Cloud Partner](https://cloudpartner.azure.com/).

1. Nell'angolo in alto a destra selezionare l’icona dell’account e quindi scegliere il **profilo di pubblicazione**.

   ![Selezionare il profilo di pubblicazione](media/marketplace-publishers-guide/guid-image-for-lu.png)

1. Nella **pagina Profilo** selezionare **Aggiungi GUID di rilevamento.**

   ![Selezionare Aggiungi GUID di rilevamento](media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

1. Nel riquadro del **GUID di rilevamento** immettere il GUID di rilevamento. Immettere solo il GUID senza il prefisso **pid-**. Nella casella della **descrizione personalizzata**, immettere il nome o la descrizione dell'offerta.

   ![Pagina del profilo](media/marketplace-publishers-guide/guid-dev-center-login.png)

   ![Immettere GUID e descrizione dell'offerta](media/marketplace-publishers-guide/guid-dev-center-example.png)

1. Per registrare più GUID, selezionare di nuovo **Add Tracking GUID** (Aggiungi GUID di rilevamento). Nella pagina verranno visualizzate finestre aggiuntive.

   ![Selezionare di nuovo Aggiungi GUID di rilevamento](media/marketplace-publishers-guide/guid-dev-center-example-add.png)

   ![Immettere un altro GUID e una descrizione dell'offerta](media/marketplace-publishers-guide/guid-dev-center-example-description.png)

1. Selezionare **Salva**.

   ![Selezionare Salva](media/marketplace-publishers-guide/guid-dev-center-save.png)

Dopo aver aggiunto il GUID per il modello o nell'agente utente e registrato il GUID nel CPP vengono tracciate tutte le distribuzioni.

## <a name="verify-the-guid-deployment"></a>Verificare la distribuzione GUID

Dopo aver modificato il modello ed eseguito un test di distribuzione, usare il seguente script di PowerShell per recuperare le risorse che sono state distribuite e a cui sono stati assegnati tag.

È possibile utilizzare lo script per verificare che il GUID sia stato aggiunto correttamente al modello di Resource Manager. Lo script non è applicabile alla distribuzione di API di Resource Manager.

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

È possibile trovare il report per l'attribuzione di utilizzo dei clienti nel dashboard di analisi di Partner Center. ([https://partner.microsoft.com/en-us/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)). Per visualizzare il report, è necessario usare le credenziali del centro per i Partner per l'accesso. Se verificano problemi con report o accedere, creare una richiesta di supporto seguendo le istruzioni nella sezione Get.

Scegliere modello rilevate dall'elenco a discesa del tipo di associazione di Partner per visualizzare il report.

![Report per l'attribuzione di utilizzo dei clienti](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Informare i clienti

I partner devono informare i clienti sulle distribuzioni che usano l'attribuzione di utilizzo dei clienti. Microsoft segnala l'utilizzo di Azure associato a queste distribuzioni al partner. Gli esempi seguenti includono i contenuti che è possibile utilizzare per notificare ai clienti queste distribuzioni. Negli esempi, sostituire \<PARTNER> con il nome della società. I partner devono garantire che la notifica sia allineata sia in linea con le politiche in materia di privacy e raccolta dei dati, comprese le opzioni di esclusione dei clienti dal rilevamento.

### <a name="notification-for-resource-manager-template-deployments"></a>Notifica per le distribuzioni dei modelli di Resource Manager

Durante la distribuzione di questo modello, Microsoft è in grado di identificare l'installazione del software del \<PARTNER> con le risorse di Azure distribuite. Microsoft è in grado di correlare le risorse di Azure usate per supportare il software. Microsoft raccoglie queste informazioni per fornire l'esperienza utente migliore con i propri prodotti e per il funzionamento delle proprie attività aziendali. Questi dati verranno raccolti e disciplinati dalle informative sulla privacy di Microsoft, che sono reperibili all’indirizzo https://www.microsoft.com/trustcenter.

### <a name="notification-for-sdk-or-api-deployments"></a>Notifica per le distribuzioni di API o SDK

Durante la distribuzione del software \<PARTNER>, Microsoft è in grado di identificare l'installazione del software del \<PARTNER> con le risorse di Azure distribuite. Microsoft è in grado di correlare le risorse di Azure usate per supportare il software. Microsoft raccoglie queste informazioni per fornire l'esperienza utente migliore con i propri prodotti e per il funzionamento delle proprie attività aziendali. Questi dati verranno raccolti e disciplinati dalle informative sulla privacy di Microsoft, che sono reperibili all’indirizzo https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Supporto

Se si verificano problemi con i report o accedere al centro per i Partner, creare una richiesta di supporto con il team di supporto di Centro per i Partner: [https://partner.microsoft.com/en-US/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Se occorre assistenza per Marketplace Onboarding e/o l'attribuzione di utilizzo dei clienti, seguire questa procedura.

1. Andare alla [pagina del supporto](https://go.microsoft.com/fwlink/?linkid=844975).

1. In **Tipo di problema**, selezionare **Marketplace Onboarding**.

1. Scegliere la **categoria** del problema:

   - Per problemi di associazione dell'utilizzo, selezionare **Altro**.
   - Per problemi di accesso con l’Azure Marketplace CPP, selezionare **Problema di accesso**.

     ![Scegliere la categoria di problema](media/marketplace-publishers-guide/lu-article-incident.png)

1. Fare clic su **Avvia richiesta**.

1. Nella pagina successiva, immettere i valori richiesti. Selezionare **continuare**.

1. Nella pagina successiva, immettere i valori richiesti.

   > [!Important]
   > Nella casella **Titolo imprevisto**, immettere **Rilevamento utilizzo IVS**. Descrivere nel dettaglio il problema riscontrato.

   ![Inserire il rilevamento di utilizzo ISV per il titolo dell'imprevisto](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Completare il modulo e quindi selezionare **Invia**.

È anche possibile ricevere indicazioni tecniche da un Microsoft Partner consulente tecnico di tecniche per la prevendita, la distribuzione e scenari di sviluppo di app per comprendere e incorporare attribuzione dell'utilizzo dei clienti.

### <a name="how-to-submit-a-technical-consultation-request"></a>Come inviare una richiesta di consulenza tecnica

1. Visita [ https://aka.ms/TechnicalJourney ](https://aka.ms/TechnicalJourney).
1. Selezionare dell'infrastruttura Cloud e la gestione e una nuova pagina verrà aperta automaticamente visualizzare il tecnico tragitto.
1. In servizi di distribuzione, fare clic su un pulsante di richiesta di invio
1. Accedere con l'account del servizio gestito (account MPN) o AAD (account del Dashboard del Partner); basato sulle credenziali di accesso, verrà aperto un modulo di richiesta online:
    * Completamento/verifica le informazioni di contatto.
    * I dettagli di consulenza possono essere pre-popolati o selezionare il menu a discesa.
    * Immettere un titolo e la descrizione del problema (specificare il livello di dettaglio possibile).
1. Fare clic su Invia

Consente di visualizzare istruzioni con screenshot nella [ https://aka.ms/TechConsultInstructions ](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>Quali sono le novità

Verrà contattati da un consulente tecnico del Partner Microsoft per configurare una chiamata a definire l'ambito le proprie esigenze.

## <a name="faq"></a>Domande frequenti

**Qual è il vantaggio dell'aggiunta di GUID al modello?**

Microsoft offre ai partner con una visualizzazione delle distribuzioni dei clienti di soluzioni e informazioni dettagliate sul loro utilizzo influenced. Microsoft e il partner possono usare queste informazioni per aumentare l'engagement tra i team di vendita. Microsoft e il partner possono anche usare i dati per ottenere un'idea più coerente dell'impatto di un singolo partner sulla crescita di Azure.

**Dopo essere stato aggiunto, un GUID può essere modificato?**

Sì, un cliente o un partner di implementazione può personalizzare il modello e può modificare o rimuovere il GUID. È consigliabile che i partner descrivono in modo proattivo il ruolo della risorsa e GUID ai propri clienti e partner per impedire la rimozione o modifica al GUID. La modifica del GUID ha effetto solo su implementazioni e risorse nuove e non esistenti.

**È possibile monitorare i modelli distribuiti da un repository non Microsoft, ad esempio GitHub?**

Sì, se il GUID è presente quando il modello viene distribuito, viene rilevato il suo utilizzo. I partner devono disporre di un profilo nel CPP per registrare i GUID utilizzati per la distribuzione all'esterno di Azure Marketplace.

**Il cliente riceve anche la funzione di creazione report?**

I clienti sono in grado di rilevare l'utilizzo di singole risorse o gruppi di risorse definiti dall'utente all'interno del portale di Azure.

**È questa metodologia simile alla Digital Partner of Record (DPOR)?**

Questo nuovo metodo per collegare la distribuzione e l'utilizzo alla soluzione di un partner fornisce un meccanismo per collegare una soluzione partner all'utilizzo di Azure. DPOR intende associare un partner di consulenza (integratore di sistemi) o di gestione (provider di servizio gestito) all'abbonamento Azure di un cliente.

**Quali vantaggi offre l'utilizzo del form per il generatore di GUID di Archiviazione di Azure?**

Il form per il generatore di GUID di Archiviazione di Azure garantisce la generazione di un GUID del formato richiesto. Se si usa uno dei metodi di rilevamento del piano dati di Archiviazione di Azure, inoltre, è possibile usare lo stesso GUID per il rilevamento del piano di controllo del Marketplace. In questo modo, è possibile usare un GUID unificato per l'attribuzione dei partner, senza dover gestire GUID separati.

**È possibile usare un disco rigido virtuale privato, personalizzato per un'offerta di modello in Azure Marketplace?**

No, non è possibile. Immagine della macchina virtuale deve provenire da Azure Marketplace, vedere: [ https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines ](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines).

È possibile creare un'offerta di macchina virtuale nel marketplace con il disco rigido virtuale personalizzato e contrassegnarlo come privata in modo che nessun altro può visualizzarlo. Quindi riferimento a questa macchina virtuale nel modello di soluzione.

**Non è stato possibile aggiornare *contentVersion* proprietà per il modello principale?**

Probabilmente un bug in alcuni casi, quando il modello viene distribuito usando un TemplateLink da un altro modello che si aspettano contentVersion meno recenti per qualche motivo. La soluzione alternativa consiste nell'usare la proprietà dei metadati:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
