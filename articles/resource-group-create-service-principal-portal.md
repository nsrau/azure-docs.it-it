<properties
   pageTitle="Creare una nuova entità servizio di Azure usando il portale di Azure"
   description="Descrive come creare una nuova entità servizio di Azure che è possibile usare con il controllo degli accessi in base al ruolo in Gestione risorse di Azure per gestire l'accesso alle risorse."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz"/>

# Creare una nuova entità servizio di Azure usando il portale di Azure

## Panoramica
Un'entità servizio è un processo, un'applicazione o un servizio automatizzato che deve accedere ad altre risorse. Usando Gestione risorse di Azure, è possibile concedere l'accesso a un'entità servizio e autenticarla in modo che possa eseguire le azioni di gestione consentite sulle risorse presenti nella sottoscrizione o come tenant.

Questo argomento mostra come creare una nuova entità servizio usando il portale di Azure. Attualmente, è necessario usare il portale di Microsoft Azure per creare una nuova entità servizio. Questa funzionalità verrà aggiunta al portale di anteprima di Azure in una versione successiva.

## Concetti
1. Azure Active Directory (AAD): servizio di gestione delle identità e degli accessi pensato per il cloud. Per altre informazioni, vedere [Informazioni su Azure Active Directory](./active-directory-whatis/)
2. Entità servizio: istanza di un'applicazione in una directory.
3. Applicazione AD: record di directory in AAD che identifica un'applicazione in AAD. Per altre informazioni, vedere [Nozioni di base sull'autenticazione in Azure AD](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).


## Creare un'applicazione di Active Directory
1. Accedere all'account di Azure tramite il [portale classico](https://manage.windowsazure.com/).

2. Selezionare **Active Directory** dal riquadro di sinistra.

   ![selezionare Active Directory][1]

3. Selezionare la directory da usare per la creazione della nuova applicazione.

   ![scegliere la directory][2]

3. Per visualizzare le applicazioni nella directory, fare clic su **Applicazioni**.

   ![visualizzare le applicazioni][11]

4. Se non è stata creata un'applicazione in questa directory, dovrebbe essere visualizzata un'immagine simile a questa riportata di seguito. Fare clic su **AGGIUNGI APPLICAZIONE**

   ![aggiungere un'applicazione][6]

   In alternativa, fare clic su **Aggiungi** nel riquadro inferiore.

   ![aggiungere][12]

5. Selezionare il tipo di applicazione da creare. Per questa esercitazione non verrà usata un'applicazione della raccolta.

   ![nuova applicazione][10]

6. Immettere il nome dell'applicazione e selezionare il tipo di applicazione da usare. Poiché si intende usare l'entità servizio dell'applicazione per l'autenticazione con Gestione risorse di Azure, creare un'**APPLICAZIONE WEB E/O API WEB** e fare clic sul pulsante Avanti.

   ![assegnare un nome all'applicazione][9]

7. Compilare le proprietà per l'app. Per **URL ACCESSO** specificare l'URI a un sito Web che descrive l'applicazione. L'esistenza del sito Web non viene convalidata. Per **URI ID APP** specificare l'URI che identifica l'applicazione. L'univocità o l'esistenza dell'endpoint non viene convalidata. Fare clic su **Completa** per creare l'applicazione AAD.

   ![proprietà dell'applicazione][4]

## Creare la password dell'entità servizio
A questo punto, nel portale dovrebbe essere selezionata l'applicazione specificata.

1. Fare clic sulla scheda **Configura** per configurare la password dell'applicazione.

   ![configura applicazione][3]

2. Scorrere fino alla sezione **Chiavi** e selezionare la durata della validità della password.

   ![chiavi][7]

3. Selezionare **Salva** per creare la chiave.

   ![salvare][13]

   Viene visualizzata la chiave salvata, che è possibile copiare.

   ![chiave salvata][8]

4. Ora è possibile usare la chiave per l'autenticazione come entità servizio. Per l'accesso è necessario l'**ID CLIENT** oltre alla **CHIAVE**. Andare sull'**ID CLIENT** e copiarlo.
  
   ![id client][5]


A questo punto, l'applicazione è pronta e l'entità servizio è stata creata nel tenant. Quando si accede come entità servizio, assicurarsi di usare:

* **ID CLIENT**: come nome utente.
* **CHIAVE**: come password.

## Passaggi successivi
Introduzione

- [Panoramica di Gestione risorse di Azure](./resource-group-overview.md)  
- [Uso di Azure PowerShell con Gestione risorse di Azure](./powershell-azure-resource-manager.md)
- [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione delle risorse di Azure](virtual-machines/xplat-cli-azure-resource-manager.md)  
- [Uso del portale di Azure per gestire le risorse di Azure](./resource-group-portal.md)  
  
Creazione e distribuzione delle applicazioni
  
- [Creazione di modelli di Gestione risorse di Azure](./resource-group-authoring-templates.md)  
- [Distribuire un'applicazione con un modello di Gestione risorse di Azure](./resource-group-template-deploy.md)  
- [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure](virtual-machines/resource-group-deploy-debug.md)  
- [Funzioni del modello di Gestione risorse di Azure](./resource-group-template-functions.md)  
- [Operazioni avanzate con i modelli](./resource-group-advanced-template.md)  
- [Distribuire le risorse di Azure usando le librerie .NET e un modello](virtual-machines/arm-template-deployment.md)
  
Organizzazione delle risorse
  
- [Uso dei tag per organizzare le risorse di Azure](./resource-group-using-tags.md)  
  
Gestione e controllo dell'accesso
  
- [Gestione e controllo dell'accesso alle risorse](./resource-group-rbac.md)  
- [Autenticazione di un'entità servizio con Gestione risorse di Azure](./resource-group-authenticate-service-principal.md)  

<!-- Images. -->
[1]: ./media/resource-group-create-service-principal-portal/active-directory.png
[2]: ./media/resource-group-create-service-principal-portal/active-directory-details.png
[3]: ./media/resource-group-create-service-principal-portal/application-configure.png
[4]: ./media/resource-group-create-service-principal-portal/app-properties.png
[5]: ./media/resource-group-create-service-principal-portal/client-id.png
[6]: ./media/resource-group-create-service-principal-portal/create-application.png
[7]: ./media/resource-group-create-service-principal-portal/create-key.png
[8]: ./media/resource-group-create-service-principal-portal/save-key.png
[9]: ./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png
[10]: ./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png
[11]: ./media/resource-group-create-service-principal-portal/view-applications.png
[12]: ./media/resource-group-create-service-principal-portal/add-icon.png
[13]: ./media/resource-group-create-service-principal-portal/save-icon.png

<!--HONumber=52-->
