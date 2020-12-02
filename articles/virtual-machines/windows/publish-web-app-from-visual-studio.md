---
title: Pubblicare un'app Web in una macchina virtuale di Azure da Visual Studio
description: Pubblicare un'applicazione Web ASP.NET in una macchina virtuale di Azure da Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 7cf2474c9ca005d85cea28f2b8fa1e23836b191f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487559"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Pubblicare un'app Web ASP.NET in una macchina virtuale di Azure da Visual Studio

Questo documento descrive come pubblicare un'applicazione Web ASP.NET in una macchina virtuale (VM) di Azure usando la funzionalità di pubblicazione **macchine virtuali di Microsoft Azure** in Visual Studio 2019.  

## <a name="prerequisites"></a>Prerequisiti
Per usare Visual Studio per pubblicare un progetto ASP.NET in una macchina virtuale di Azure, la macchina virtuale deve essere configurata correttamente.

- Il computer deve essere configurato per eseguire un'applicazione Web ASP.NET ed è installato WebDeploy. Per altre informazioni, vedere [creare una VM ASP.NET con WebDeploy](https://github.com/aspnet/Tooling/blob/AspNetVMs/docs/create-asp-net-vm-with-webdeploy.md).

- Per la macchina virtuale deve essere configurato un nome DNS. Per altre informazioni, vedere [Creare un nome di dominio completo nel portale di Azure per una macchina virtuale Windows](../create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Pubblicare l'app Web ASP.NET nella macchina virtuale di Azure con Visual Studio
La sezione seguente descrive come pubblicare un'applicazione Web ASP.NET esistente in una macchina virtuale di Azure.

1. Aprire la soluzione app Web in Visual Studio 2019.
2. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Pubblica**.
3. Usare la freccia a destra della pagina per scorrere le opzioni di pubblicazione fino a trovare **Macchine virtuali di Microsoft Azure**.  

   ![Pagina di pubblicazione - Freccia a destra]

4. Selezionare l'icona **Macchine virtuali di Microsoft Azure** e quindi **Pubblica**.

   ![Pagina di pubblicazione - Icona Macchine virtuali di Microsoft Azure]

5. Scegliere l'account appropriato (con la sottoscrizione di Azure connessa alla macchina virtuale).  
   - Se è stato effettuato l'accesso a Visual Studio, l'elenco di account viene popolato con tutti gli account autenticati.  
   - Se non è stato effettuato l'accesso o se l'account necessario non è elencato, scegliere "Aggiungi un account" e seguire le istruzioni per l'accesso.  
   ![Selezione account di Azure]  

6. Selezionare la macchina virtuale appropriata nell'elenco di macchine virtuali esistenti.

   > [!Note]
   > Il popolamento di questo elenco può richiedere tempo.

   ![Selezione macchina virtuale di Azure]

7. Fare clic su OK per avviare la pubblicazione.

8. Quando vengono richieste le credenziali, fornire il nome utente e la password di un account utente nella macchina virtuale di destinazione configurata con diritti di pubblicazione. Queste credenziali sono in genere il nome utente amministratore e la password usati durante la creazione della macchina virtuale.  

   ![Accesso a WebDeploy]

9. Accettare il certificato di protezione.

   ![Errore del certificato]

10. Esaminare la finestra di output per controllare l'avanzamento dell'operazione di pubblicazione.

    ![Finestra di output]

11. Se la pubblicazione ha esito positivo, viene avviato un browser per aprire l'URL del sito appena pubblicato.

**Operazione riuscita.**

L'app Web è stata pubblicata correttamente in una macchina virtuale di Azure.

## <a name="publish-page-options"></a>Opzioni della pagina di pubblicazione

Dopo avere completato la procedura guidata di pubblicazione, viene aperta nel documento la pagina di pubblicazione con il nuovo profilo di pubblicazione selezionato.

### <a name="re-publish"></a>Eseguire di nuovo la pubblicazione

Per pubblicare gli aggiornamenti all'applicazione Web, selezionare il pulsante **Pubblica** nella pagina di pubblicazione.  
- Se richiesto, immettere nome utente e password.  
- La pubblicazione viene avviata immediatamente.

![Pagina di pubblicazione - Pulsante Pubblica]

### <a name="modify-publish-profile-settings"></a>Modificare le impostazioni del profilo di pubblicazione

Per visualizzare e modificare le impostazioni del profilo di pubblicazione, selezionare **Impostazioni**.  

![Pagina di pubblicazione - Pulsante Impostazioni]

Le impostazioni saranno simili a quanto segue:  

![Impostazioni di pubblicazione - Pagina di connessione]

#### <a name="save-user-name-and-password"></a>Salvare nome utente e password
- Evitare di fornire informazioni di autenticazione ogni volta che si pubblica. A tale scopo, popolare i campi **nome utente** e **password** e selezionare la casella **Salva password** .
- Usare il pulsante **Convalida connessione** per verificare di avere immesso le informazioni esatte.

#### <a name="deploy-to-clean-web-server"></a>Eseguire la distribuzione in un server Web pulito

- Per assicurarsi che il server Web disponga di una copia pulita dell'applicazione Web dopo ogni caricamento e che nessun altro file venga lasciato da una distribuzione precedente, è possibile selezionare la casella di controllo **Rimuovi file aggiuntivi nella destinazione** nella scheda **Impostazioni** .

- Avviso: se si esegue la pubblicazione con questa impostazione vengono eliminati tutti i file presenti nel server Web (directory wwwroot). Assicurarsi di conoscere lo stato del computer prima di eseguire la pubblicazione con questa opzione abilitata. 

![Impostazioni di pubblicazione - Pagina Impostazioni]

## <a name="next-steps"></a>Passaggi successivi

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Configurare l'integrazione continua e la distribuzione continua per la distribuzione automatica in una macchina virtuale di Azure

Per configurare una pipeline di recapito continuo con Azure Pipelines, vedere [Eseguire la distribuzione in una macchina virtuale Windows](/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Pagina di pubblicazione - Freccia a destra]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Pagina di pubblicazione - Icona Macchine virtuali di Microsoft Azure]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Selezione account di Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Selezione macchina virtuale di Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Accesso a WebDeploy]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Errore del certificato]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Finestra di output]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Pagina di pubblicazione - Pulsante Pubblica]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Pagina di pubblicazione - Pulsante Impostazioni]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Impostazioni di pubblicazione - Pagina di connessione]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Impostazioni di pubblicazione - Pagina Impostazioni]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png