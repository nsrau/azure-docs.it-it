---
title: Attribuzione dell'uso da parte dei clienti e dei partner di Azure
description: Panoramica su come tenere traccia dell'uso da parte dei clienti per le soluzioni di Azure Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/26/2018
ms.author: ellacroi
ms.openlocfilehash: 46cd5dbc044cbd0b7e38e5f0d0c8aa1916387a2d
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038191"
---
# <a name="azure-partner-customer-usage-attribution"></a>Attribuzione dell'uso da parte dei clienti e dei partner di Azure

Le soluzioni software dei partner per Azure richiedono componenti di Azure o devono essere implementate direttamente sull'infrastruttura di Azure.  Attualmente, quando un cliente distribuisce una soluzione di partner e fornisce le proprie risorse di Azure, è difficile per il partner ottenere visibilità sullo stato di tali distribuzioni e ottenere informazioni sull'impatto sulla crescita dell'infrastruttura di Azure. L'aggiunta di un livello di visibilità maggiore aiuta i partner ad allinearsi con i team di vendita Microsoft e ottenere credito per i programmi per partner Microsoft.   

Microsoft sta creando un nuovo metodo che consente ai partner di rilevare meglio l'utilizzo di Azure grazie alla distribuzione del software su Azure da parte di un cliente. Questo nuovo metodo è basato sull'uso di Azure Resource Manager per orchestrare la distribuzione dei servizi di Azure.

I partner Microsoft possono associare l'utilizzo di Azure a qualsiasi risorsa Azure fornita per conto di un cliente.  Questa associazione può essere eseguita tramite Azure Marketplace, il repository di Guida introduttiva, il repository di GitHub privato e anche con engagement dei clienti 1 su 1.  Per tenere traccia dell'associazione, sono disponibili due approcci:

- Modelli di Azure Resource Manager: i modelli di Azure Resource Manager o i modelli di soluzione per distribuire i servizi Azure al fine di eseguire il software del partner. I partner possono creare i modelli di Azure Resource Manager che definiscono l'infrastruttura e la configurazione della soluzione Azure. Creando un modello di Azure Resource Manager è possibile anche per i clienti distribuire la soluzione nel corso del ciclo di vita della stessa garantendo al contempo che le risorse vengano distribuite in uno stato coerente. 

- API di Azure Resource Manager: i partner possono chiamare le API di Azure Resource Manager direttamente per distribuire un modello di Azure Resource Manager o per generare le chiamate API per la fornitura diretta di servizi Azure. 

## <a name="method-1-azure-resource-manager-templates"></a>Metodo 1: Modelli di Azure Resource Manager 

Oggi, molte soluzioni dei partner vengono distribuite nella sottoscrizione del cliente usando i modelli di Azure Resource Manager.  Se si dispone già di un modello di Azure Resource Manager presente in Azure Marketplace, in GitHub o come Guida introduttiva, il processo di modifica del modello per abilitare questo nuovo metodo di rilevamento deve essere semplice.  Se non si usa un modello di Azure Resource Manager, ecco alcuni collegamenti che consentono una migliore comprensione dei modelli di Azure Resource Manager e spiegano come crearne uno: 

*   [Creare e distribuire il primo modello di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Guida alla creazione di un modello di soluzione per Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="instructions-add-a-guid-to-your-existing-azure-resource-manager-template"></a>Istruzioni: aggiungere un GUID al modello di Azure Resource Manager esistente

L’aggiunta di un GUID è una singola modifica al file del modello principale:
 1. Creare un GUID, supponendo come valore generato eb7927c8-dd66-43e1-b0cf-c346a422063
 2. Aprire il modello di Azure Resource Manager
 3. Aggiungere una nuova risorsa nel file del modello principale. La risorsa deve essere solo in mainTemplate.json o azuredeploy.json, non in un template annidato o collegato.
 4. Immettere il GUID dopo "pid-", come illustrato in precedenza.

   Dovrebbe essere visualizzata una schermata analoga al seguente esempio: `pid-eb7927c8-dd66-43e1-b0cf-c346a422063`

 5. Controllare il modello per individuare eventuali errori
 6. Pubblicare nuovamente il modello nel repository appropriato

## <a name="sample-template-code"></a>Codice del modello di esempio

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/tracking-sample-code-for-lu-1.PNG?token=Ak8ZDB0JzsBdUGlKEIeHNJRS7b0BWn4Gks5bbMwwwA%3D%3D)


## <a name="method-2-azure-resource-manager-apis"></a>Metodo 2: API di Azure Resource Manager

In alcuni casi, è preferibile effettuare chiamate direttamente alle API REST di Azure Resource Manager per distribuire i servizi di Azure. [Azure supporta più SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) per abilitare questa opzione.  È possibile usare uno degli SDK o chiamare le API REST direttamente per distribuire le risorse.

Se si usa un modello Azure Resource Manager, è consigliabile assegnare tag alla soluzione usando le istruzioni riportate sopra.  Se non si utilizza un modello di Azure Resource Manager ed è possibile effettuare chiamate dirette alle API, è comunque possibile assegnare tag all'installazione remota per associare l'utilizzo delle risorse di Azure. 

**Come assegnare tag a una distribuzione usando l'API di Azure Resource Manager:** per questo approccio, quando si progettano le chiamate all'API si includerà un GUID nell'intestazione agente utente all’interno della richiesta. Il GUID deve essere aggiunto per ogni offerta o SKU.  La stringa deve essere formattata con il prefisso pid - e quindi includere il GUID generato dal partner.   

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG?token=Ak8ZDDiokRcj4PJj0aMkZmfF8BdOuOTzks5bbM35wA%3D%3D)

>[!Note] 
>Formato GUID da inserire nell'agente utente: pid-eb7927c8-dd66-43e1-b0cf-c346a422063 // inserire il proprio GUID dopo "pid-"

Il formato della stringa è importante. Se non è incluso il prefisso "pid-", non è possibile eseguire query sui dati. Diversi SDK eseguono questo processo in modo diverso.  Per implementare questo metodo è necessario rivedere il supporto e l'approccio per l'SDK Azure che si preferisce. 

**Esempio di utilizzo di Python SDK:** per Python, è necessario usare l'attributo "config". È possibile aggiungere un GUID solo a un agente utente. Di seguito è fornito un esempio: 

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/python-for-lu.PNG?token=Ak8ZDK5Um4J6oY-7x25tuBpa168BEiYMks5bbMuUwA%3D%3D)

>Questa azione deve essere eseguita per ogni client, non c'è una configurazione statica globale (si può scegliere di eseguire un factory client per essere sicuri che ogni client lo stia eseguendo). 
>[Informazioni di riferimento aggiuntive](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)

Come assegnare tag a una distribuzione usando Azure PowerShell o l'interfaccia della riga di comando di Azure: se si distribuiscono risorse tramite AzurePowerShell, è possibile aggiungere il GUID usando il metodo seguente:

```

[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")


```

Per aggiungere il proprio GUID quando si usa l’interfaccia della riga di comando di Azure di Azure, impostare la variabile di ambiente AZURE_HTTP_USER_AGENT.  È possibile impostare questa variabile nell'ambito di uno script o a livello globale, per l'uso dell'ambito di applicazione della shell:

```

export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'


```

## <a name="registering-guidsoffers"></a>Registrazione di GUID/offerte

Affinché il GUID possa essere incluso nel rilevamento, deve essere registrato.  

Tutte le registrazioni per i modelli GUID verranno eseguite tramite il portale Cloud Partner (CPP) di Azure Marketplace. 

1. Registrarsi ad [Azure Marketplace](http://aka.ms/listonazuremarketplace) oggi stesso e accedere al portale Cloud Partner.

 *  I partner dovranno [disporre di un profilo in CPP](https://docs.microsoft.com/azure/marketplace/become-publisher) e saranno invitati a inserire l'offerta in Azure Marketplace o AppSource 
 *  I partner saranno in grado di registrare più GUID 
 *  I partner saranno anche in grado di registrare una GUID per i modelli/offerte di soluzioni non di marketplace
 
2. Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
3. Nell'angolo in alto a destra del portale fare clic sull'icona dell'account e quindi su **Publisher profile** (Profilo di pubblicazione)

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-image-for-lu.png)

4. Nella pagina Profile (Profilo) fare clic su **Add Tracking GUID** (Aggiungi GUID di rilevamento).

![](https://github.com/ellacroi/azure-docs-pr/blob/last-lu-images-i-hope/articles/marketplace/media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

5. Nel campo espanso immettere il GUID di rilevamento (solo il GUID, senza il prefisso "pid-") nel campo **Tracking GUID** (GUID di rilevamento) e il nome o la descrizione dell'offerta nel campo **Custom Description** (Descrizione personalizzata).

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-login.png)

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example.png)

6. Per registrare più GUID, fare di nuovo clic su **Add Tracking GUID** (Aggiungi GUID di rilevamento). Verrà visualizzato un altro campo espanso. 

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example-add.png)

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example-description.png)

7. Al termine delle modifiche, fare clic su **Save** (Salva) per salvare le modifiche apportate. 

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-save.png)



## <a name="verification-of-guid-deployment"></a>Verifica della distribuzione del GUID 

Dopo aver modificato il modello ed eseguito un test di distribuzione, è possibile usare il seguente script di PowerShell per recuperare le risorse che sono state distribuite e a cui sono stati assegnati tag. 

È possibile utilizzarlo per verificare se il GUID è stato aggiunto al modello di Azure Resource Manager correttamente. Non è applicabile alla distribuzione di API di Azure Resource Manager.

Accedere ad Azure e selezionare la sottoscrizione che contiene la distribuzione che si vuole verificare prima di eseguire lo script. L'esecuzione deve avvenire nel contesto della sottoscrizione della distribuzione.

Il GUID e il nome resourceGroup della distribuzione sono parametri necessari.

È possibile trovare lo script originale [qui](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1).

```

Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName'
)

#get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

#find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

#find all deploymentOperations in a deployment by name (since PowerShell does not surface outputResources on the deployment or correlationId on the deploymentOperation)

foreach ($deployment in $deployments){

#get deploymentOperations by deploymentName and then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}


```

## <a name="guidance-on-creating-guids"></a>Materiale sussidiario sulla creazione di GUID

Un GUID (identificatore univoco globale) è un numero di riferimento univoco 32 cifre esadecimali. Per creare un GUID, è necessario usare un generatore di GUID per la creazione di GUID per il rilevamento.  Sono disponibili più [generatori GUID online](https://www.bing.com/search?q=guid%20generator&qs=n&form=QBRE&sp=-1&ghc=2&pq=guid%20g&sc=8-6&sk=&cvid=0BAFAFCD70B34E4296BB97FBFA3E1B4E).

Si consiglia di creare un GUID univoco per ogni canale di offerta e distribuzione,  ad esempio se si dispone di due soluzioni ed entrambe vengono distribuite tramite un modello e sono disponibili sia in Azure Marketplace che in GitHub.  Creare quattro GUID:

*   Offerta A su Azure Marketplace 
*   Offerta A su GitHub
*   Offerta B su Azure Marketplace 
*   Offerta B su GitHub

La creazione di report verrà eseguita dal partner (ID Partner Microsoft) e GUID. 

È anche possibile scegliere di tracciare le GUID a un livello più granulare, ovvero a livello di SKU (dove le SKU sono varianti di un'offerta).

## <a name="guidance-on-privacy-and-data-collection"></a>Indicazioni sulla raccolta dei dati e la privacy

I partner devono fornire messaggi per informare i propri clienti che le distribuzioni che includono il monitoraggio del GUID di Azure Resource Manager consentiranno a Microsoft di segnalare l'utilizzo di Azure associato a tali distribuzioni al partner.  Di seguito viene riportato qualche esempio. Dove viene indicato "PARTNER" è necessario inserire il nome della propria azienda. Inoltre, i partner devono garantire che la lingua corrisponda alle proprie informative sulla privacy e sulla raccolta dei dati, comprese le opzioni di esclusione dei clienti dal rilevamento: 

**Per le distribuzioni dei modelli di Azure Resource Manager**

Durante la distribuzione di questo modello, Microsoft sarà in grado di identificare l'installazione del software del PARTNER con le risorse di Azure distribuite.  Microsoft sarà in grado di correlare le risorse di Azure usate per supportare il software.  Microsoft raccoglie queste informazioni per fornire l'esperienza utente migliore con i propri prodotti e per il funzionamento delle proprie attività aziendali. Questi dati verranno raccolti e disciplinati dalle informative sulla privacy di Microsoft, che sono reperibili all’indirizzo https://www.microsoft.com/trustcenter. 

**Per le distribuzioni di API o SDK**

Durante la distribuzione del software del PARTNER, Microsoft sarà in grado di identificare l'installazione del software del PARTNER con le risorse di Azure distribuite.  Microsoft sarà in grado di correlare le risorse di Azure usate per supportare il software.  Microsoft raccoglie queste informazioni per fornire l'esperienza utente migliore con i propri prodotti e per il funzionamento delle proprie attività aziendali. Questi dati verranno raccolti e disciplinati dalle informative sulla privacy di Microsoft, che sono reperibili all’indirizzo https://www.microsoft.com/trustcenter.

## <a name="support"></a>Supporto

Per ottenere assistenza, seguire questi passaggi:
 1. Visita la pagina del supporto disponibile all'indirizzo [go.microsoft.com/fwlink/?linkid=844975](https://go.microsoft.com/fwlink/?linkid=844975)
 2. Per problemi relativi all'associazione dell'utilizzo: selezionare tipo di problema: **Marketplace Onboarding** e categoria: **Altro** e quindi fare clic su **Avvia richiesta.** 

Per problemi relativi all'accesso al portale Cloud Partner di Azure Marketplace: selezionare tipo di problema: **Marketplace Onboarding** e categoria: **Problema di accesso** e quindi fare clic su **Avvia richiesta.**

 ![](https://github.com/ellacroi/azure-docs-pr/blob/last-lu-images-i-hope/articles/marketplace/media/marketplace-publishers-guide/lu-article-incident.png)
 

 3. Completare i campi obbligatori nella pagina successiva e fare clic su **Continua.**
 4. Completare i campi di testo libero nella pagina successiva. **Importante**: come titolo dell'evento imprevisto specificare **"ISV Usage Tracking"** (Rilevamento dell'utilizzo di ISV) e descrivere il problema in modo dettagliato nell'ampio campo di testo libero successivo.  Completare il resto del modulo e fare clic su **Invia**. 
 
  ![](https://github.com/qianw211/azure-docs-pr/blob/MyImgAdded-2/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-help.png)

 

## <a name="faqs"></a>Domande frequenti

**Qual è il vantaggio dell'aggiunta di GUID al modello?**

Microsoft fornirà ai partner una panoramica delle distribuzioni dei clienti dei propri modelli e informazioni dettagliate sull'uso che ne è stato influenzato.  Microsoft e il partner possono usare queste informazioni anche per aumentare l'engagement tra i team di vendita. Microsoft e il partner possono anche usare questi dati per ottenere un'idea più coerente dell'impatto di un singolo partner sulla crescita di Azure. 

**Chi può aggiungere un GUID a un modello?**

La risorsa di rilevamento è destinata a collegare la soluzione del partner all'uso di Azure da parte dei clienti.  I dati di utilizzo sono legati all'identità Microsoft Partner Network (MPN ID) di un partner e i report saranno disponibili per i partner nel portale Cloud Partner (CPP).  

**Dopo essere stato aggiunto, un GUID può essere modificato?**
 
Sì, un cliente o un partner di implementazione può personalizzare il modello e può modificare o rimuovere il GUID. Si consiglia ai partner di descrivere in modo proattivo il ruolo della risorsa e del GUID ai propri clienti e al partner per impedire la rimozione o la modifica del GUID di rilevamento.  La modifica del GUID avrà effetto solo su implementazioni e risorse nuove e non esistenti.

**Quando sarà disponibile la funzione di creazione report?**

Una versione beta della funzione di creazione report sarà disponibile a breve.  La funzione di creazione report verrà integrata nel portale Cloud Partner (CPP).

**È possibile monitorare i modelli distribuiti da un repository non Microsoft, ad esempio GitHub?**

Sì, se il GUID è presente quando il modello viene distribuito, verrà rilevato il suo utilizzo.  
I partner devono disporre di un profilo nel portale Cloud Partner per registrare i relativi modelli pubblicati al di fuori di Azure Marketplace. 

**C'è differenza se il modello viene distribuito da Azure Marketplace rispetto ad altri repository come GitHub?**

Sì, i partner che pubblicano offerte su Azure Marketplace possono ricevere dati più dettagliati sulle implementazioni da Azure Marketplace.  I partner trarranno vantaggio dall'esposizione della loro offerta ai clienti sul portale Azure Marketplace e sul portale di gestione di Azure. Le offerte nel marketplace di Azure generano anche potenziali clienti per il partner.

**Cosa accade se si crea un modello personalizzato per un engagement dei clienti singoli?**

Si può comunque aggiungere il GUID al modello.  Se si utilizza un GUID esistente che è stato registrato, verrà incluso nei report.  Se si crea un nuovo GUID, sarà necessario registrarlo per includerlo nel rilevamento.

**Il cliente riceve anche la funzione di creazione report?**

I clienti sono attualmente in grado di rilevare l'utilizzo di singole risorse o gruppi di risorse definiti dall'utente all'interno del portale di gestione di Azure.   

**Questa metodologia di rilevamento è simile a DPOR(Digital Partner of Record)?**

Questo nuovo metodo per collegare la distribuzione e l'utilizzo alla soluzione di un partner ha lo scopo di fornire un meccanismo per collegare una soluzione partner all'utilizzo di Azure.  DPOR intende associare un partner di consulenza (integratore di sistemi) o di gestione (provider di servizio gestito) all'abbonamento Azure di un cliente.   
