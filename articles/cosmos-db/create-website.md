---
title: Distribuire un'app Web con un modello - Azure Cosmos DB | Microsoft Docs
description: Informazioni su come distribuire un account Azure Cosmos DB, app Web del servizio app di Azure e un'applicazione Web di esempio usando un modello di Azure Resource Manager.
services: cosmos-db, app-service\web
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 087d8786-1155-42c7-924b-0eaba5a8b3e0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: 89513d6c1b9aa9f4709359d6d7681bff9c291618
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Distribuire Azure Cosmos DB e app Web del servizio app di Azure tramite un modello di Azure Resource Manager
Questa esercitazione illustra come usare un modello di Azure Resource Manager per distribuire e integrare [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), un'app Web del [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) e un'applicazione Web di esempio.

Con il modello di Azure Resource Manager è possibile automatizzare facilmente la distribuzione e la configurazione delle risorse di Azure.  Questa esercitazione illustra come distribuire un'applicazione Web e configurare automaticamente le informazioni relative alla connessione dell'account Azure Cosmos DB.

Dopo aver completato questa esercitazione, si potrà rispondere alle domande seguenti:  

* Come è possibile usare un modello di Azure Resource Manager per distribuire e integrare un account Azure Cosmos DB e un'app Web nel servizio app di Azure?
* Come si usa un modello di Azure Resource Manager per distribuire e integrare un account Azure Cosmos DB, un'app Web del servizio app e un'applicazione WebDeploy?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Prerequisiti
> [!TIP]
> Anche se questa esercitazione non presuppone alcuna esperienza nell'uso di JSON o dei modelli di Azure Resource Manager, se si vogliono modificare i modelli o le opzioni di distribuzione cui viene fatto riferimento, è necessario conoscere ciascuna di queste aree.
> 
> 

Prima di seguire le istruzioni di questa esercitazione, verificare di avere una sottoscrizione di Azure. Azure è una piattaforma basata su sottoscrizione.  Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto](https://azure.microsoft.com/pricing/purchase-options/), [Offerte per i membri](https://azure.microsoft.com/pricing/member-offers/) oppure [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a id="CreateDB"></a>Passaggio 1: Scaricare i file del modello
Per iniziare, scaricare i file di modello necessari per questa esercitazione.

1. Scaricare il modello di [esempio per creare un account Azure Cosmos DB e app Web e per distribuire un'applicazione demo](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) in una cartella locale, ad esempio C:\Azure Cosmos DBTemplates. Questo modello distribuisce un account Azure Cosmos DB, un'app Web del servizio app e un'applicazione Web.  L'applicazione Web viene anche configurata automaticamente per connettersi all'account Azure Cosmos DB.
2. Scaricare il modello di [esempio per creare un account Azure Cosmos DB e app Web](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) in una cartella locale, ad esempio C:\Azure Cosmos DBTemplates. Questo modello distribuisce un account Azure Cosmos DB e un'app Web del servizio app e inoltre modifica le impostazioni dell'applicazione del sito per rilevare con facilità le informazioni relative alla connessione di Azure Cosmos DB. Non è tuttavia inclusa alcuna applicazione Web.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Passaggio 2: Distribuire l'account Azure Cosmos DB, l'app Web del servizio app e l'applicazione demo di esempio
Si procederà ora alla distribuzione del primo modello.

> [!TIP]
> Il modello non verifica che il nome dell'app Web e quello dell'account Azure Cosmos DB specificati siano validi e disponibili.  È consigliabile verificare la disponibilità dei nomi che si intende fornire prima della distribuzione.
> 
> 

1. Accedere al [portale di Azure](https://portal.azure.com), fare clic su Nuovo e ricercare "Distribuzione modello".
    ![Screenshot dell'interfaccia utente della distribuzione del modello](./media/create-website/TemplateDeployment1.png)
2. Selezionare la voce Distribuzione modello e fare clic su **Crea** ![Screenshot dell'interfaccia utente della distribuzione del modello](./media/create-website/TemplateDeployment2.png).
3. Fare clic su **Modifica modello**, incollare il contenuto del file del modello DocDBWebsiteTodo.json e fare clic su **Salva**.
   ![Screenshot dell'interfaccia utente della distribuzione del modello](./media/create-website/TemplateDeployment3.png)
4. Fare clic su **Modifica parametri**, specificare i valori per ogni parametro obbligatorio e fare clic su **OK**.  I parametri sono i seguenti:
   
   1. SITENAME: specifica il nome dell'app Web del servizio app e viene usato per creare l'URL che consente di accedere all'app Web. Se ad esempio si specifica "mydemodocdbwebapp", l'URL usato per accedere all'app Web è mydemodocdbwebapp.azurewebsites.net.
   2. HOSTINGPLANNAME: specifica il nome del piano di hosting del servizio app da creare.
   3. LOCATION: specifica la posizione di Azure in cui creare le risorse di Azure Cosmos DB e dell'app Web.
   4. DATABASEACCOUNTNAME: specifica il nome dell'account Azure Cosmos DB da creare.   
      
      ![Screenshot dell'interfaccia utente della distribuzione del modello](./media/create-website/TemplateDeployment4.png)
5. Scegliere un gruppo di risorse esistente o fornire un nome per creare un nuovo gruppo di risorse, quindi selezionare la relativa posizione.

    ![Screenshot dell'interfaccia utente della distribuzione del modello](./media/create-website/TemplateDeployment5.png)
6. Fare clic su **Rivedere le note legali**, **Acquista**, quindi su **Crea** per iniziare la distribuzione.  Selezionare **Aggiungi al dashboard** per rendere facilmente visibile la distribuzione ottenuta nella home page del portale di Azure.
   ![Screenshot dell'interfaccia utente della distribuzione del modello](./media/create-website/TemplateDeployment6.png)
7. Al termine della distribuzione, viene aperto il riquadro del gruppo di risorse.
   ![Screenshot del riquadro del gruppo di risorse](./media/create-website/TemplateDeployment7.png)  
8. Per usare l'applicazione, passare all'URL dell'app Web. Nell'esempio precedente l'URL sarebbe http://mydemodocdbwebapp.azurewebsites.net.  Verrà visualizzata l'applicazione Web seguente:
   
   ![Applicazione di esempio](./media/create-website/image2.png)
9. Proseguire e creare qualche attività nell'app Web, quindi tornare al riquadro del gruppo di risorse nel portale di Azure. Fare clic sulla risorsa dell'account Azure Cosmos DB nell'elenco delle risorse e quindi su **Esplora dati**.
10. Eseguire la query predefinita "SELECT * FROM c" ed esaminarne i risultati.  Si noti che la query ha recuperato la rappresentazione JSON delle attività create nel passaggio 7 precedente.  È possibile provare a eseguire query, ad esempio SELECT * FROM c WHERE c.isComplete = true per restituire tutti gli elementi todo contrassegnati come completati.
11. È possibile verificare la funzionalità del portale di Azure Cosmos DB o modificare l'applicazione Todo di esempio.  A questo punto si è pronti per distribuire un altro modello.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Passaggio 3: Distribuire l'esempio relativo ad account DocumentDB e app Web
Si procederà ora alla distribuzione del secondo modello.  Questo modello è utile per mostrare come inserire le informazioni relative alla connessione di Azure Cosmos DB, ad esempio l'endpoint dell'account e la chiave master in un'app Web come impostazioni dell'applicazione o come stringa di connessione personalizzata. Si supponga ad esempio di avere un'applicazione Web che si intende distribuire con un account Azure Cosmos DB e con informazioni relative alla connessione popolate automaticamente durante la distribuzione.

> [!TIP]
> Il modello non verifica che il nome dell'app Web e il nome dell'account Azure Cosmos DB siano validi e disponibili.  È consigliabile verificare la disponibilità dei nomi che si intende fornire prima della distribuzione.
> 
> 

1. Nel [portale di Azure](https://portal.azure.com), fare clic su Nuovo e ricercare "Distribuzione modello".
    ![Screenshot dell'interfaccia utente della distribuzione del modello](./media/create-website/TemplateDeployment1.png)
2. Selezionare la voce Distribuzione modello e fare clic su **Crea** ![Screenshot dell'interfaccia utente della distribuzione del modello](./media/create-website/TemplateDeployment2.png).
3. Fare clic su **Modifica modello**, incollare il contenuto del file del modello DocDBWebSite.json e fare clic su **Salva**.
   ![Screenshot dell'interfaccia utente della distribuzione del modello](./media/create-website/TemplateDeployment3.png)
4. Fare clic su **Modifica parametri**, specificare i valori per ogni parametro obbligatorio e fare clic su **OK**.  I parametri sono i seguenti:
   
   1. SITENAME: specifica il nome dell'app Web del servizio app e viene usato per creare l'URL che consente di accedere all'app Web. Se ad esempio si specifica "mydemodocdbwebapp", l'URL usato per accedere all'app Web è mydemodocdbwebapp.azurewebsites.net.
   2. HOSTINGPLANNAME: specifica il nome del piano di hosting del servizio app da creare.
   3. LOCATION: specifica la posizione di Azure in cui creare le risorse di Azure Cosmos DB e dell'app Web.
   4. DATABASEACCOUNTNAME: specifica il nome dell'account Azure Cosmos DB da creare.   
      
      ![Screenshot dell'interfaccia utente della distribuzione del modello](./media/create-website/TemplateDeployment4.png)
5. Scegliere un gruppo di risorse esistente o fornire un nome per creare un nuovo gruppo di risorse, quindi selezionare la relativa posizione.

    ![Screenshot dell'interfaccia utente della distribuzione del modello](./media/create-website/TemplateDeployment5.png)
6. Fare clic su **Rivedere le note legali**, **Acquista**, quindi su **Crea** per iniziare la distribuzione.  Selezionare **Aggiungi al dashboard** per rendere facilmente visibile la distribuzione ottenuta nella home page del portale di Azure.
   ![Screenshot dell'interfaccia utente della distribuzione del modello](./media/create-website/TemplateDeployment6.png)
7. Al termine della distribuzione, viene aperto il riquadro del gruppo di risorse.
   ![Screenshot del riquadro del gruppo di risorse](./media/create-website/TemplateDeployment7.png)  
8. Fare clic sulla risorsa dell'App Web nell'elenco delle risorse, quindi fare clic su **Impostazioni applicazione** ![Screenshot del gruppo di risorse](./media/create-website/TemplateDeployment9.png).  
9. Si noti la presenza di impostazioni dell'applicazione per l'endpoint di Azure Cosmos DB e per ognuna delle relative chiavi master.

    ![Screenshot delle impostazioni dell'applicazione](./media/create-website/TemplateDeployment10.png)  
10. È possibile continuare a esplorare il portale di Azure o seguire uno degli [esempi](http://go.microsoft.com/fwlink/?LinkID=402386) di Azure Cosmos DB per creare la propria applicazione Azure Cosmos DB.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Passaggi successivi
Congratulazioni! È stata completata la distribuzione di Azure Cosmos DB, di un'app Web del servizio app e di un'applicazione Web di esempio usando i modelli di Azure Resource Manager.

* Per altre informazioni su Azure Cosmos DB, fare clic [qui](http://azure.com/docdb).
* Per altre informazioni sulle app Web del servizio app di Azure, vedere [qui](http://go.microsoft.com/fwlink/?LinkId=325362).
* Per altre informazioni sui modelli di Gestione risorse di Azure, vedere [qui](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

