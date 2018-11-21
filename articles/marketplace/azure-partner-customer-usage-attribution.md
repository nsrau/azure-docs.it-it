---
title: Attribuzione dell'uso da parte dei clienti e dei partner di Azure
description: Panoramica su come tenere traccia dell'uso da parte dei clienti per le soluzioni di Azure Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: yijenj
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: yijenj
ms.openlocfilehash: 3a1c5341e391c8be1af42eea940fbf147b88e7c8
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685702"
---
# <a name="azure-partner-customer-usage-attribution"></a>Attribuzione dell'uso da parte dei clienti e dei partner di Azure

Le soluzioni software dei partner per Azure richiedono componenti di Azure o devono essere implementate direttamente sull'infrastruttura di Azure. I clienti che implementano la soluzione di un partner e forniscono le proprie risorse Azure possono avere difficoltà a ottenere visibilità sullo stato dell'implementazione e a ottenere informazioni dettagliate relative all'impatto sulla crescita di Azure. L'aggiunta di un livello di visibilità maggiore aiuta ad allinearsi con i team di vendita Microsoft e ottenere credito per i programmi per partner Microsoft.   

Microsoft offre ora un metodo per aiutare i partner a monitorare meglio l'uso di Azure delle distribuzioni dei clienti del loro software su Azure. Questo nuovo metodo si basa sull'uso di Azure Resource Manager per orchestrare la distribuzione dei servizi di Azure.

I partner Microsoft possono associare l'utilizzo di Azure a qualsiasi risorsa Azure fornita per conto di un cliente. È possibile creare l'associazione tramite Azure Marketplace, il repository Quickstart, i repository privati GitHub e il coinvolgimento individuale dei clienti. Per tenere traccia dell'associazione, sono disponibili due approcci:

- Modelli di Azure Resource Manager: i modelli di Resource Manager o i modelli di soluzione per distribuire i servizi Azure al fine di eseguire il software del partner. I partner possono creare i modelli di Resource Manager che definiscono l'infrastruttura e la configurazione della soluzione Azure. Un modello di Resource Manager consente a partner e rispettivi clienti di distribuire la propria soluzione per tutto il suo ciclo di vita. È possibile essere certi che le risorse vengano distribuite in uno stato coerente. 

- API di Azure Resource Manager: i partner possono chiamare le API di Resource Manager direttamente per distribuire un modello di Resource Manager o per generare le chiamate API per la fornitura diretta di servizi Azure. 

## <a name="use-resource-manager-templates"></a>Usare i modelli di Resource Manager

Molte soluzioni dei partner vengono distribuite nella sottoscrizione del cliente usando i modelli di Resource Manager. Se si dispone di un modello di Resource Manager presente in Azure Marketplace, in GitHub o come una Guida introduttiva, il processo di modifica del modello per abilitare questo nuovo metodo di rilevamento deve essere semplice. Se non si usa un modello di Azure Resource Manager, ecco alcuni collegamenti che consentono una migliore comprensione dei modelli di Resource Manager e spiegano come crearne uno: 

*   [Creare e distribuire il primo modello di Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Creare un modello di soluzione per Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

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

Per questo approccio di rilevamento, quando si progettano le chiamate API, includere un GUID nell'intestazione agente utente nella richiesta. Aggiungere il GUID per ogni offerta o SKU. Formattare la stringa con il prefisso **pid-** e includere il GUID generato dal partner. Di seguito è riportato un esempio del formato GUID da inserire nell'agente utente: 

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

```
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Aggiungere un tag a una distribuzione usando l’interfaccia della riga di comando di Azure

Quando si utilizza Azure CLI per aggiungere il proprio GUID, impostare la variabile di ambiente **AZURE_HTTP_USER_AGENT**. È possibile impostare questa variabile nell'ambito di uno script. È anche possibile impostare la variabile a livello globale per l'ambito della shell:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

## <a name="create-guids"></a>Crea GUID

Un GUID è un numero di riferimento univoco con 32 cifre esadecimali. Per creare un GUID per il rilevamento, è necessario usare un generatore di GUID. Il team di Archiviazione di Azure ha creato un [modulo generatore di GUID](https://aka.ms/StoragePartners) che invierà un'e-mail con un GUID nel formato corretto e riutilizzabile nei vari sistemi di rilevamento. 

> [!Note]
> Per la creazione di questo GUID è altamente consigliato l'uso del [form per il generatore di GUID di Archiviazione di Azure](https://aka.ms/StoragePartners). Per altre informazioni, vedere la sezione [Domande frequenti](#faq).

Si consiglia di creare un GUID univoco per ogni canale di offerta e distribuzione per ogni prodotto. È possibile scegliere di usare un unico GUID per i vari canali di distribuzione del prodotto se non si vuole suddividere la creazione di report. 

Se si usa un modello per distribuire un prodotto disponibile su Azure Marketplace e su GitHub, è possibile creare e registrare 2 GUID distinti:

*   Prodotto A su Azure Marketplace 
*   Prodotto A su GitHub

La creazione di report viene effettuata in base al valore partner (ID partner Microsoft) e ai GUID. 

È anche possibile tracciare i GUID a un livello più granulare, come a livello di SKU (dove le SKU sono varianti di un'offerta).

## <a name="register-guids-and-offers"></a>Registrare GUID e offerte

Per includere un GUID nel rilevamento, il GUID deve essere registrato.  

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

```
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="notify-your-customers"></a>Informare i clienti

I partner devono informare i propri clienti sulle distribuzioni che utilizzano il rilevamento del GUID di Resource Manager. Microsoft segnala l'utilizzo di Azure associato a queste distribuzioni al partner. Gli esempi seguenti includono i contenuti che è possibile utilizzare per notificare ai clienti queste distribuzioni. Negli esempi, sostituire \<PARTNER> con il nome della società. I partner devono garantire che la notifica sia allineata sia in linea con le politiche in materia di privacy e raccolta dei dati, comprese le opzioni di esclusione dei clienti dal rilevamento. 

### <a name="notification-for-resource-manager-template-deployments"></a>Notifica per le distribuzioni dei modelli di Resource Manager

Durante la distribuzione di questo modello, Microsoft è in grado di identificare l'installazione del software del \<PARTNER> con le risorse di Azure distribuite. Microsoft è in grado di correlare le risorse di Azure usate per supportare il software. Microsoft raccoglie queste informazioni per fornire l'esperienza utente migliore con i propri prodotti e per il funzionamento delle proprie attività aziendali. Questi dati verranno raccolti e disciplinati dalle informative sulla privacy di Microsoft, che sono reperibili all’indirizzo https://www.microsoft.com/trustcenter. 

### <a name="notification-for-sdk-or-api-deployments"></a>Notifica per le distribuzioni di API o SDK

Durante la distribuzione del software \<PARTNER>, Microsoft è in grado di identificare l'installazione del software del \<PARTNER> con le risorse di Azure distribuite. Microsoft è in grado di correlare le risorse di Azure usate per supportare il software. Microsoft raccoglie queste informazioni per fornire l'esperienza utente migliore con i propri prodotti e per il funzionamento delle proprie attività aziendali. Questi dati verranno raccolti e disciplinati dalle informative sulla privacy di Microsoft, che sono reperibili all’indirizzo https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Supporto

Se è necessaria assistenza, seguire questa procedura.

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

## <a name="faq"></a>Domande frequenti

**Qual è il vantaggio dell'aggiunta di GUID al modello?**

Microsoft fornisce ai partner una panoramica delle distribuzioni dei clienti dei propri modelli e informazioni dettagliate sull'uso che ne è stato influenzato. Microsoft e il partner possono usare queste informazioni per aumentare l'engagement tra i team di vendita. Microsoft e il partner possono anche usare i dati per ottenere un'idea più coerente dell'impatto di un singolo partner sulla crescita di Azure. 

**Chi può aggiungere un GUID a un modello?**

La risorsa di rilevamento è destinata a collegare la soluzione del partner all'uso di Azure da parte dei clienti. I dati di utilizzo sono legati all'identità di un partner in Microsoft Partner Network (MPN ID). La creazione di report è disponibile per i partner nel CPP.

**Dopo essere stato aggiunto, un GUID può essere modificato?**
 
Sì, un cliente o un partner di implementazione può personalizzare il modello e può modificare o rimuovere il GUID. Si consiglia ai partner di descrivere in modo proattivo il ruolo della risorsa e del GUID ai propri clienti e al partner per impedire la rimozione o la modifica del GUID di rilevamento. La modifica del GUID ha effetto solo su implementazioni e risorse nuove e non esistenti.

**Quando sarà disponibile la funzione di creazione report?**

Una versione beta della funzione di creazione report sarà disponibile a breve. La funzione di creazione report verrà integrata nel portale CPP.

**È possibile monitorare i modelli distribuiti da un repository non Microsoft, ad esempio GitHub?**

Sì, se il GUID è presente quando il modello viene distribuito, viene rilevato il suo utilizzo. I partner devono disporre di un profilo nel portale CPP per registrare i relativi modelli pubblicati al di fuori di Azure Marketplace. 

**C'è differenza se il modello viene distribuito da Azure Marketplace rispetto ad altri repository come GitHub?**

Sì, i partner che pubblicano offerte su Azure Marketplace possono ricevere dati più dettagliati sulle implementazioni da Azure Marketplace. I partner traggono vantaggio dall'esposizione della loro offerta ai clienti sul portale Azure Marketplace e sul portale di Azure. Le offerte nel Marketplace di Azure generano anche potenziali clienti per il partner.

**Cosa accade se si crea un modello personalizzato per un engagement dei clienti singoli?**

Si può comunque aggiungere il GUID al modello. Se si utilizza un GUID già registrato, questo viene incluso nella creazione del report. Se si crea un nuovo GUID, è necessario registrarlo per includerlo nel rilevamento.

**Il cliente riceve anche la funzione di creazione report?**

I clienti sono in grado di rilevare l'utilizzo di singole risorse o gruppi di risorse definiti dall'utente all'interno del portale di Azure.   

**Questa metodologia di rilevamento è simile a DPOR(Digital Partner of Record)?**

Questo nuovo metodo per collegare la distribuzione e l'utilizzo alla soluzione di un partner fornisce un meccanismo per collegare una soluzione partner all'utilizzo di Azure. DPOR intende associare un partner di consulenza (integratore di sistemi) o di gestione (provider di servizio gestito) all'abbonamento Azure di un cliente.   

**Quali vantaggi offre l'utilizzo del form per il generatore di GUID di Archiviazione di Azure?**

Il form per il generatore di GUID di Archiviazione di Azure garantisce la generazione di un GUID del formato richiesto. Se si usa uno dei metodi di rilevamento del piano dati di Archiviazione di Azure, inoltre, è possibile usare lo stesso GUID per il rilevamento del piano di controllo del Marketplace. In questo modo, è possibile usare un GUID unificato per l'attribuzione dei partner, senza dover gestire GUID separati.
