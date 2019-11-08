---
title: Attribuzione del partner e dell'utilizzo dei clienti di Azure | Azure Marketplace
description: Panoramica su come tenere traccia dell'uso da parte dei clienti per le soluzioni di Azure Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 9/23/2019
ms.author: pabutler
ms.openlocfilehash: c84f5538d2f553a713b52aa795a10acddac9aff8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819880"
---
# <a name="azure-partner-customer-usage-attribution"></a>Attribuzione dell'uso da parte dei clienti e dei partner di Azure

Le soluzioni software dei partner per Azure richiedono componenti di Azure o devono essere implementate direttamente sull'infrastruttura di Azure. I clienti che implementano la soluzione di un partner e forniscono le proprie risorse Azure possono avere difficoltà a ottenere visibilità sullo stato dell'implementazione e a ottenere informazioni dettagliate relative all'impatto sulla crescita di Azure. L'aggiunta di un livello di visibilità maggiore aiuta ad allinearsi con i team di vendita Microsoft e ottenere credito per i programmi per partner Microsoft.

Microsoft offre ora un metodo per aiutare i partner a monitorare meglio l'uso di Azure delle distribuzioni dei clienti del loro software su Azure. Questo nuovo metodo si basa sull'uso di Azure Resource Manager per orchestrare la distribuzione dei servizi di Azure.

I partner Microsoft possono associare l'utilizzo di Azure a qualsiasi risorsa Azure fornita per conto di un cliente. È possibile creare l'associazione tramite Azure Marketplace, il repository Quickstart, i repository privati GitHub e il coinvolgimento individuale dei clienti. L'attribuzione dell'utilizzo del cliente supporta tre opzioni di distribuzione:

- Modelli di Azure Resource Manager: i partner possono usare i modelli Gestione risorse per distribuire i servizi di Azure per eseguire il software del partner. I partner possono creare i modelli di Resource Manager che definiscono l'infrastruttura e la configurazione della soluzione Azure. Un modello di Resource Manager consente a partner e rispettivi clienti di distribuire la propria soluzione per tutto il suo ciclo di vita. È possibile essere certi che le risorse vengano distribuite in uno stato coerente.
- API di Azure Resource Manager: i partner possono chiamare le API di Resource Manager direttamente per distribuire un modello di Resource Manager o per generare le chiamate API per la fornitura diretta di servizi Azure.
- Bonifica: i partner possono usare l'agente di orchestrazione cloud, ad esempio la bonifica, per distribuire un modello di Gestione risorse o distribuire direttamente i servizi di Azure

L'attribuzione dell'utilizzo dei clienti riguarda la nuova distribuzione e non supporta l'assegnazione di tag alle risorse esistenti che sono già state distribuite.

L'attribuzione dell'utilizzo del cliente è necessaria per [applicazione Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer): offerta del modello di soluzione pubblicata in Azure Marketplace.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>Usare i modelli di Resource Manager
Molte soluzioni partner vengono distribuite nella sottoscrizione di un cliente usando modelli di Gestione risorse. Se si dispone di un modello di Gestione risorse disponibile in Azure Marketplace, su GitHub o come guida introduttiva, il processo di modifica del modello per consentire l'attribuzione dell'utilizzo dei clienti dovrebbe essere semplice.

Per altre informazioni sulla creazione e sulla pubblicazione di modelli di soluzione, vedere

* [Creare e distribuire il primo modello di Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
* [Offerta di applicazione Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer).
* Video: [creazione di modelli di soluzioni e applicazioni gestite per Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


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

Per abilitare l'attribuzione dell'utilizzo del cliente, quando si progettano le chiamate API, includere un GUID nell'intestazione agente utente nella richiesta. Aggiungere il GUID per ogni offerta o SKU. Formattare la stringa con il prefisso **pid-** e includere il GUID generato dal partner. Di seguito è riportato un esempio del formato GUID da inserire nell'agente utente:

![Esempio di formato GUID](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> Il formato della stringa è importante. Se non è incluso il prefisso **pid-** , non è possibile eseguire query sui dati. Diversi SDK eseguono il rilevamento in modo diverso. Per implementare questo metodo è necessario rivedere il supporto e l'approccio di rilevamento per l'SDK Azure che si preferisce.

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
Per ulteriori informazioni, vedere [Azure SDK per go](https://docs.microsoft.com/azure/go/).

## <a name="use-terraform"></a>USA bonifica

Il supporto per la bonifica è disponibile tramite la versione 1.21.0 del provider di Azure: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Questo supporto si applica a tutti i partner che distribuiscono la propria soluzione tramite bonifica e tutte le risorse distribuite e a consumo dal provider di Azure (versione 1.21.0 o successiva).

Il provider di Azure per la bonifica ha aggiunto un nuovo campo facoltativo denominato [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) , che consente di specificare il GUID di rilevamento usato per la soluzione. Il valore di questo campo può anche essere originato dalla variabile di ambiente *ARM_PARTNER_ID* .

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
I partner che desiderano ottenere la distribuzione tramite la bonifica registrata dall'attribuzione dell'utilizzo dei clienti devono eseguire le operazioni seguenti:

* Creare un GUID (il GUID deve essere aggiunto per ogni offerta o SKU)
* Aggiornare il provider di Azure per impostare il valore di *partner_id* sul GUID (non pre-correggere il GUID con "PID-", impostarlo semplicemente sul GUID effettivo)

## <a name="create-guids"></a>Crea GUID

Un GUID è un numero di riferimento univoco con 32 cifre esadecimali. Per creare un GUID per il rilevamento, è necessario usare un generatore di GUID. Il team di Archiviazione di Azure ha creato un [modulo generatore di GUID](https://aka.ms/StoragePartners) che invierà un'e-mail con un GUID nel formato corretto e riutilizzabile nei vari sistemi di rilevamento.

> [!Note]
> È consigliabile usare il [modulo Generatore GUID di archiviazione di Azure](https://aka.ms/StoragePartners) per creare il GUID. Per altre informazioni, vedere la sezione [Domande frequenti](#faq).

Si consiglia di creare un GUID univoco per ogni canale di offerta e distribuzione per ogni prodotto. È possibile scegliere di usare un unico GUID per i vari canali di distribuzione del prodotto se non si vuole suddividere la creazione di report.

Se si usa un modello per distribuire un prodotto disponibile su Azure Marketplace e su GitHub, è possibile creare e registrare 2 GUID distinti:

*   Prodotto A su Azure Marketplace
*   Prodotto A su GitHub

La creazione di report viene effettuata in base al valore partner (ID partner Microsoft) e ai GUID.

È anche possibile tracciare i GUID a un livello più granulare, come a livello di SKU (dove le SKU sono varianti di un'offerta).

## <a name="register-guids-and-offers"></a>Registrare GUID e offerte

I GUID devono essere registrati per consentire l'attribuzione dell'utilizzo da parte del cliente.

Tutte le registrazioni per i GUID del modello vengono eseguite all'interno del centro per i partner.

Dopo aver aggiunto il GUID al modello o all'agente utente e aver registrato il GUID in Partner Center, viene tenuta traccia di tutte le distribuzioni.

1. Iscriversi come [editore del Marketplace commerciale](https://aka.ms/JoinMarketplace).

   * È necessario che i partner [dispongano di un profilo nel centro per i partner](https://docs.microsoft.com/azure/marketplace/become-publisher). Si consiglia di inserire l'offerta in Azure Marketplace o AppSource.
   * I partner possono registrare più GUID.
   * I partner possono un GUID per i modelli e le offerte di soluzioni non di Marketplace.

1. Accedere al centro per i [partner](https://partner.microsoft.com/dashboard).

1. Nell'angolo in alto a destra selezionare l'icona dell'ingranaggio impostazioni e quindi selezionare **Impostazioni sviluppatore**.

1. Nella **pagina Impostazioni account**selezionare **Aggiungi GUID di rilevamento.**

1. Nella casella **GUID** immettere il GUID di rilevamento. Immettere solo il GUID senza il prefisso **pid-** . Nella casella **Descrizione** immettere il nome dell'offerta o la descrizione.

1. Per registrare più GUID, selezionare di nuovo **Add Tracking GUID** (Aggiungi GUID di rilevamento). Nella pagina verranno visualizzate finestre aggiuntive.

1. Selezionare **Salva**.


## <a name="verify-the-guid-deployment"></a>Verificare la distribuzione GUID

Dopo aver modificato il modello ed eseguito un test di distribuzione, usare il seguente script di PowerShell per recuperare le risorse che sono state distribuite e a cui sono stati assegnati tag.

È possibile utilizzare lo script per verificare che il GUID sia stato aggiunto correttamente al modello di Resource Manager. Lo script non si applica alle distribuzioni Gestione risorse API o di bonifica.

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

È possibile trovare il report per l'attribuzione dell'utilizzo dei clienti nel dashboard del centro per i partner. ([https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)). Per visualizzare il report, è necessario usare le credenziali del centro per i partner per accedere. Se si verificano problemi con il report o l'accesso, creare una richiesta di supporto seguendo le istruzioni nella sezione ottenere supporto.

Scegliere modello rilevato nell'elenco a discesa del tipo di associazione partner per visualizzare il report.

![Report per l'attribuzione dell'utilizzo dei clienti](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Informare i clienti

I partner devono informare i clienti sulle distribuzioni che usano l'attribuzione dell'utilizzo dei clienti. Microsoft segnala l'utilizzo di Azure associato a queste distribuzioni al partner. Gli esempi seguenti includono i contenuti che è possibile utilizzare per notificare ai clienti queste distribuzioni. Negli esempi, sostituire \<PARTNER> con il nome della società. I partner devono garantire che la notifica sia allineata sia in linea con le politiche in materia di privacy e raccolta dei dati, comprese le opzioni di esclusione dei clienti dal rilevamento.

### <a name="notification-for-resource-manager-template-deployments"></a>Notifica per le distribuzioni dei modelli di Resource Manager

Durante la distribuzione di questo modello, Microsoft è in grado di identificare l'installazione del software del \<PARTNER> con le risorse di Azure distribuite. Microsoft è in grado di correlare le risorse di Azure usate per supportare il software. Microsoft raccoglie queste informazioni per fornire l'esperienza utente migliore con i propri prodotti e per il funzionamento delle proprie attività aziendali. Questi dati verranno raccolti e disciplinati dalle informative sulla privacy di Microsoft, che sono reperibili all’indirizzo https://www.microsoft.com/trustcenter.

### <a name="notification-for-sdk-or-api-deployments"></a>Notifica per le distribuzioni di API o SDK

Durante la distribuzione del software \<PARTNER>, Microsoft è in grado di identificare l'installazione del software del \<PARTNER> con le risorse di Azure distribuite. Microsoft è in grado di correlare le risorse di Azure usate per supportare il software. Microsoft raccoglie queste informazioni per fornire l'esperienza utente migliore con i propri prodotti e per il funzionamento delle proprie attività aziendali. Questi dati verranno raccolti e disciplinati dalle informative sulla privacy di Microsoft, che sono reperibili all’indirizzo https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Supporto

Sono disponibili due canali di supporto in base ai problemi riscontrati.

Se si verificano problemi nel centro per i partner, ad esempio per visualizzare il report di attribuzione dell'utilizzo del cliente o per accedere, creare una richiesta di supporto con il team di supporto di partner Center qui: [https://partner.microsoft.com/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Se è necessaria assistenza per l'onboarding del Marketplace e/o l'attribuzione dell'utilizzo dei clienti in generale, ad esempio come configurare l'attribuzione dell'utilizzo del cliente, attenersi alla procedura seguente:

1. Andare alla [pagina del supporto](https://go.microsoft.com/fwlink/?linkid=844975).

1. In **Tipo di problema**, selezionare **Marketplace Onboarding**.

1. Scegliere la **categoria** del problema:

   - Per problemi di associazione dell'utilizzo, selezionare **Altro**.
   - Per problemi di accesso con l’Azure Marketplace CPP, selezionare **Problema di accesso**.

     ![Scegliere la categoria di problema](media/marketplace-publishers-guide/lu-article-incident.png)

1. Fare clic su **Avvia richiesta**.

1. Nella pagina successiva, immettere i valori richiesti. Selezionare **Continua**.

1. Nella pagina successiva, immettere i valori richiesti.

   > [!Important]
   > Nella casella **Titolo imprevisto**, immettere **Rilevamento utilizzo IVS**. Descrivere nel dettaglio il problema riscontrato.

   ![Inserire il rilevamento di utilizzo ISV per il titolo dell'imprevisto](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Completare il modulo e quindi selezionare **Invia**.

Per comprendere e incorporare l'attribuzione dell'utilizzo dei clienti, è inoltre possibile ricevere indicazioni tecniche da un consulente tecnico Microsoft per gli scenari di sviluppo tecnico, distribuzione e app.

### <a name="how-to-submit-a-technical-consultation-request"></a>Come inviare una richiesta di consultazione tecnica

1. Visitare [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney).
1. Selezionare infrastruttura e gestione cloud. per visualizzare il percorso tecnico, viene aperta una nuova pagina.
1. In servizi di distribuzione fare clic sul pulsante Invia una richiesta
1. Accedere usando il servizio MSA (account MPN) o AAD (account dashboard partner); in base alle credenziali di accesso, si aprirà un modulo di richiesta online:
    * Completa/controlla le informazioni di contatto.
    * I dettagli della consultazione potrebbero essere precompilati o selezionare gli elenchi a discesa.
    * Immettere un titolo e la descrizione del problema (fornire il maggior dettaglio possibile).
1. Fare clic su Invia

Vedere le istruzioni dettagliate con screenshot in [https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>Passaggi successivi

Si verrà contattati da un consulente tecnico Microsoft partner per configurare una chiamata per definire l'ambito delle proprie esigenze.

## <a name="faq"></a>domande frequenti

**Qual è il vantaggio dell'aggiunta di GUID al modello?**

Microsoft offre ai partner una visualizzazione delle distribuzioni dei clienti delle proprie soluzioni e informazioni approfondite sull'utilizzo influenzato. Microsoft e il partner possono usare queste informazioni per aumentare l'engagement tra i team di vendita. Microsoft e il partner possono anche usare i dati per ottenere un'idea più coerente dell'impatto di un singolo partner sulla crescita di Azure.

**Dopo essere stato aggiunto, un GUID può essere modificato?**

Sì, un cliente o un partner di implementazione può personalizzare il modello e può modificare o rimuovere il GUID. Si consiglia ai partner di descrivere in modo proattivo il ruolo della risorsa e il GUID ai clienti e ai partner per impedire la rimozione o la modifica del GUID. La modifica del GUID ha effetto solo su implementazioni e risorse nuove e non esistenti.

**È possibile monitorare i modelli distribuiti da un repository non Microsoft, ad esempio GitHub?**

Sì, se il GUID è presente quando il modello viene distribuito, viene rilevato il suo utilizzo. Per registrare i GUID usati per la distribuzione all'esterno di Azure Marketplace, è necessario che i partner dispongano di un profilo nel CPP.

**Il cliente riceve anche la funzione di creazione report?**

I clienti sono in grado di rilevare l'utilizzo di singole risorse o gruppi di risorse definiti dall'utente all'interno del portale di Azure.

**Questa metodologia è simile a Digital Partner of record (DPOR)?**

Questo nuovo metodo per collegare la distribuzione e l'utilizzo alla soluzione di un partner fornisce un meccanismo per collegare una soluzione partner all'utilizzo di Azure. DPOR intende associare un partner di consulenza (integratore di sistemi) o di gestione (provider di servizio gestito) all'abbonamento Azure di un cliente.

**Quali vantaggi offre l'utilizzo del form per il generatore di GUID di Archiviazione di Azure?**

Il form per il generatore di GUID di Archiviazione di Azure garantisce la generazione di un GUID del formato richiesto. Se si usa uno dei metodi di rilevamento del piano dati di Archiviazione di Azure, inoltre, è possibile usare lo stesso GUID per il rilevamento del piano di controllo del Marketplace. In questo modo, è possibile usare un GUID unificato per l'attribuzione dei partner, senza dover gestire GUID separati.

**Posso usare un VHD privato e personalizzato per un'offerta di modello di soluzione in Azure Marketplace?**

No, non è possibile. L'immagine della macchina virtuale deve provenire da Azure Marketplace, vedere: [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines).

È possibile creare un'offerta di macchina virtuale in Marketplace usando il disco rigido virtuale personalizzato e contrassegnarla come privata in modo che non possa essere visualizzata da nessuno. Quindi, fare riferimento a questa macchina virtuale nel modello di soluzione.

**Non è stato possibile aggiornare la proprietà *contentVersion* per il modello principale?**

Probabilmente un bug in alcuni casi quando il modello viene distribuito usando un TemplateLink da un altro modello che prevede un contentVersion precedente per qualche motivo. La soluzione alternativa consiste nell'usare la proprietà Metadata:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
