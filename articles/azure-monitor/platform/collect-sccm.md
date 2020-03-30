---
title: Connettere Configuration Manager a Monitoraggio di Azure . Documenti Microsoft
description: Questo articolo illustra i passaggi per connettere Configuration Manager all'area di lavoro in Monitoraggio di Azure e iniziare ad analizzare i dati.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 3140c0de6fbe090e3d040202cd581c455f03b6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655257"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Connettere Configuration Manager a Monitoraggio AzureConnect Configuration Manager to Azure Monitor
È possibile connettere l'ambiente di Microsoft Endpoint Configuration Manager ad Monitoraggio di Azure per sincronizzare i dati di raccolta dei dispositivi e fare riferimento a queste raccolte in Monitoraggio di Azure e Automazione di Azure.You can connect your Microsoft Endpoint Configuration Manager environment to Azure Monitor to sync device collection data and reference these collections in Azure Monitor and Azure Automation.  

## <a name="prerequisites"></a>Prerequisiti

Monitoraggio di Azure supporta il ramo corrente di Configuration Manager, la versione 1606 e successive.

>[!NOTE]
>La funzionalità per connettere Configuration Manager con un'area di lavoro di Log Analytics è facoltativa e non è abilitata per impostazione predefinita. Pertanto sarà necessario abilitarla prima di poterla usare. Per altre informazioni, vedere [Enable optional features from updates](https://docs.microsoft.com/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Panoramica della configurazione

The following steps summarize the steps to configure Configuration Manager integration with Azure Monitor.  

1. In Azure Active Directory registrare Configuration Manager come applicazione Web e/o app API Web e verificare di disporre dell'ID client e della chiave segreta client della registrazione da Azure Active Directory. Vedere [Usare il portale per creare un'applicazione Active Directory e un'entità servizio che accedono alle risorse](../../active-directory/develop/howto-create-service-principal-portal.md) per informazioni dettagliate su come eseguire questo passaggio.

2. In Azure Active Directory concedere a [Configuration Manager (l'app Web registrata) l'autorizzazione per accedere a Monitoraggio di Azure.](#grant-configuration-manager-with-permissions-to-log-analytics)

3. In Configuration Manager aggiungere una connessione usando la procedura guidata Servizi di **Azure.In** Configuration Manager, add a connection using the Azure Services wizard.

4. [Scaricare e installare l'agente di Log Analytics per Windows](#download-and-install-the-agent) nel computer che esegue il ruolo del sistema del sistema del sito del punto di connessione del servizio di Configuration Manager.Download and install the Log Analytics agent for Windows on the computer running the Configuration Manager service connection point site system role. The agent sends Configuration Manager data to the Log Analytics workspace in Azure Monitor.

5. In Monitoraggio di Azure [importare raccolte da Configuration Manager](#import-collections) come gruppi di computer.

6. In Monitoraggio di Azure visualizzare i dati da Configuration Manager come [gruppi di computer](computer-groups.md).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Fornire a Configuration Manager le autorizzazioni per Log Analytics

Nella procedura seguente si concede il ruolo *Collaboratore* nell'area di lavoro Log Analytics all'applicazione AD e all'entità servizio creata in precedenza per Configuration Manager. Se non si dispone già di un'area di lavoro, vedere [Creare un'area di lavoro in Monitoraggio di Azure](../../azure-monitor/learn/quick-create-workspace.md) prima di procedere. In questo modo, si consente a Configuration Manager di eseguire l'autenticazione e connettersi all'area di lavoro Log Analytics.  

> [!NOTE]
> È necessario specificare le autorizzazioni nell'area di lavoro di Log Analytics per Configuration Manager.You must specify permissions in the Log Analytics workspace for Configuration Manager. In caso contrario, viene visualizzato un messaggio di errore quando si usa la procedura guidata di configurazione in Configuration Manager.
>

1. Nel portale di Azure fare clic su **Tutti i servizi** nell'angolo superiore sinistro. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.

2. Nell'elenco di aree di lavoro di Log Analytics selezionare l'area di lavoro da modificare.

3. Nel riquadro a sinistra selezionare **Controllo di accesso (IAM)**.

4. Nella pagina Controllo di accesso (IAM) fare clic su **Aggiungi un'assegnazione di ruolo**. Viene visualizzato il riquadro **Aggiungi un'assegnazione di ruolo**.

5. Nel riquadro **Aggiungi un'assegnazione di ruolo**, nell'elenco a discesa **Ruolo** selezionare il ruolo **Collaboratore**.  

6. Nell'elenco a discesa **Assegna accesso a** selezionare l'applicazione di Configuration Manager creata in precedenza in AD e quindi fare clic su **OK**.  

## <a name="download-and-install-the-agent"></a>Scaricare e installare l'agente

Esaminare l'articolo [Connettere computer Windows a Monitoraggio di Azure per](agent-windows.md) comprendere i metodi disponibili per l'installazione dell'agente di Log Analytics per Windows nel computer che ospita il ruolo del sistema del sistema del sito del punto di connessione del servizio di Configuration Manager.Review the article Connect Windows computers to Azure Monitor in Azure to understand the methods available for installing the Log Analytics agent for Windows on the computer hosting the Configuration Manager service connection point site system role.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Connettere Configuration Manager all'area di lavoro di Log AnalyticsConnect Configuration Manager to Log Analytics workspace

>[!NOTE]
> Per aggiungere una connessione di Log Analytics, l'ambiente di Configuration Manager deve disporre di un [punto di connessione](https://docs.microsoft.com/configmgr/core/servers/deploy/configure/about-the-service-connection-point) del servizio configurato per la modalità online.

> [!NOTE]
> È necessario connettere il sito di primo livello nella gerarchia ad Monitoraggio di Azure.You must connect the top-tier site in your hierarchy to Azure Monitor. Se si connette un sito primario autonomo ad Azure Monitor e quindi si aggiunge un sito di amministrazione centrale all'ambiente, è necessario eliminare e ricreare la connessione all'interno della nuova gerarchia.

1. Nell'area di lavoro **Amministrazione** di Configuration Manager selezionare **Servizi cloud** e quindi **Servizi di Azure.** 

2. Fare clic con il pulsante destro del mouse su **Servizi di Azure** e quindi scegliere Configura servizi di **Azure**. Viene visualizzata la pagina **Configura servizi di Azure.The Configure Azure Services** page appears. 
   
3. Nella schermata **Generale**, confermare di aver eseguito le azioni seguenti e di aver ottenuto i dettagli per ogni elemento, quindi selezionare **Avanti**.

4. Nella pagina Servizi di Azure della procedura guidata per i servizi di Azure:

    1. Specificare un **Nome** per l'oggetto in Configuration Manager.
    2. Specificare una **descrizione** facoltativa per identificare il servizio.
    3. Selezionare il servizio di Azure **OMS Connector**.

    >[!NOTE]
    >OMS is now referred to as Log Analytics which is a feature of Azure Monitor.

5. Selezionare **Avanti** per passare alla pagina Proprietà dell'app della procedura guidata per i servizi di Azure.

6. Nella pagina **App** della procedura guidata Servizi di Azure selezionare innanzitutto l'ambiente Azure dall'elenco e quindi fare clic su **Importa**.

7. Nella pagina **Importa app** specificare le informazioni seguenti:

    1. Specificare il **nome del tenant** di Azure AD per l'app.

    2. Specificare per **l'ID tenant** di Azure AD il tenant di Azure AD. Queste informazioni sono disponibili nella pagina Proprietà di Azure Active Directory.You can find this information on the Azure Active Directory **Properties** page. 

    3. Specificare per **Nome applicazione** il nome dell'applicazione.

    4. Specificare per **ID client**, l'ID applicazione dell'app Azure AD creata creata in precedenza.

    5. Specificare per **Chiave segreta**, la chiave segreta client dell'app Azure AD creata.

    6. Specificare per **Scadenza chiave segreta**, la data di scadenza della chiave.

    7. Specificare per **L'URI dell'ID app**, l'URI DELL'ID app dell'app Azure AD creata creata in precedenza.

    8. Selezionare **Verifica** e a destra i risultati dovrebbero essere **visualizzati come verificati con successo!**.

8. Nella pagina **Configurazione** esaminare le informazioni per verificare che le sottoscrizioni di **Azure,** il gruppo di risorse di **Azure**e i campi **dell'area** di lavoro di Operations Management Suite siano precompilati a indicare che l'applicazione Azure AD dispone di autorizzazioni sufficienti nel gruppo di risorse. Se i campi sono vuoti, indica che l'applicazione non dispone dei diritti necessari. Selezionare le raccolte di dispositivi da raccogliere e inoltrare all'area di lavoro, quindi selezionare **Aggiungi**.

9. Esaminare le opzioni nella pagina **Conferma impostazioni** e selezionare **Avanti** per iniziare a creare e configurare la connessione.

10. Al termine della configurazione, viene visualizzata la pagina **Completamento.** Selezionare **Chiudi**. 

Dopo aver collegato Configuration Manager ad Monitoraggio di Azure, è possibile aggiungere o rimuovere raccolte e visualizzare le proprietà della connessione.

## <a name="update-log-analytics-workspace-connection-properties"></a>Aggiornare le proprietà di connessione dell'area di lavoro di Log AnalyticsUpdate Log Analytics workspace connection properties

Se una password o una chiave del segreto client scade o viene persa, sarà necessario aggiornare manualmente le proprietà di connessione di Log Analytics.If a password or client secret key expires or losts, you'll need to manually update the Log Analytics connection properties.

1. Nell'area di lavoro **Amministrazione** di Configuration Manager selezionare **Servizi cloud** e quindi **Connettore OMS** per aprire la pagina **Proprietà connessione OMS.**
2. In questa pagina, fare clic sulla scheda **Azure Active Directory** per visualizzare il **tenant**, l'**ID client** e la **scadenza della chiave privata client**. **Verificare** se la **chiave privata client** è scaduta.

## <a name="import-collections"></a>Importare le raccolte

Dopo aver aggiunto una connessione di Log Analytics a Configuration Manager e installato l'agente nel computer che esegue il ruolo del sistema del sistema del sito del punto di connessione del servizio di Configuration Manager, il passaggio successivo consiste nell'importare le raccolte da Configuration Manager in AzureAfter you've added a Log Analytics connection to Configuration Manager and installed the agent on the computer running the Configuration Manager service connection point site system role, the next step is to import collections from Configuration Manager in Azure Monitorare come gruppi di computer.

Dopo aver completato la configurazione iniziale per importare le raccolte di dispositivi dalla gerarchia, le informazioni sulla raccolta vengono recuperate ogni 3 ore per mantenere aggiornata l'appartenenza. È possibile scegliere di disabilitare questa opzione in qualsiasi momento.

1. Nel portale di Azure fare clic su **Tutti i servizi** nell'angolo superiore sinistro. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.
2. Nell'elenco delle aree di lavoro di Log Analytics selezionare l'area di lavoro con cui è stato registrato Configuration Manager.  
3. Selezionare **Impostazioni avanzate**.
4. Selezionare **Gruppi di computer** e quindi **SCCM**.  
5. Selezionare **Importa appartenenze alla raccolta di Configuration Manager** e fare clic su **Salva**.  
   
    ![Gruppi di computer - Scheda SCCM](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Visualizzare i dati da Configuration Manager

Dopo aver aggiunto una connessione di Log Analytics a Configuration Manager e installato l'agente nel computer che esegue il ruolo del sistema del sistema del sito del punto di connessione del servizio di Configuration Manager, i dati dell'agente vengono inviati all'area di lavoro di Log Analytics in Monitoraggio di Azure.After you've added a Log Analytics connection to Configuration Manager and installed the agent on the computer running the Configuration Manager service connection point site system, data from the agent is sent to the Log Analytics workspace in Azure Monitor. In Monitoraggio di Azure le raccolte di Configuration Manager vengono visualizzate come [gruppi di computer.](../../azure-monitor/platform/computer-groups.md) È possibile visualizzare i gruppi dalla pagina **Configuration Manager** in **Impostazioni\Gruppi di computer**.

Una volta importate le raccolte, è possibile vedere quanti computer con appartenenze delle raccolte sono stati rilevati. È anche possibile visualizzare il numero di raccolte importate.

![Gruppi di computer - Scheda SCCM](./media/collect-sccm/sccm-computer-groups02.png)

Quando si fa clic su uno dei due, viene aperto l'editor di query del log che visualizza tutti i gruppi importati o tutti i computer che appartengono a ciascun gruppo. Utilizzando [Ricerca log](../../azure-monitor/log-query/log-query-overview.md), è possibile eseguire un'analisi più approfondita dei dati di appartenenza alla raccolta.

## <a name="next-steps"></a>Passaggi successivi

Usare [Ricerca Log](../../azure-monitor/log-query/log-query-overview.md) per visualizzare le informazioni dettagliate sui dati di Configuration Manager.
