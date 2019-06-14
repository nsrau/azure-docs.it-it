---
title: Connettere Configuration Manager a monitoraggio di Azure | Microsoft Docs
description: Questo articolo illustra i passaggi per connettere Configuration Manager all'area di lavoro in Monitoraggio di Azure e avviare l'analisi dei dati.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: 26ddb0cdd2728f9dff5d45494a14841cdc1a20cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922890"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Connettere Configuration Manager a monitoraggio di Azure
È possibile connettere l'ambiente di System Center Configuration Manager a monitoraggio di Azure per sincronizzare i dati di raccolta di dispositivi e fare riferimento a queste raccolte in Monitoraggio di Azure e automazione di Azure.  

## <a name="prerequisites"></a>Prerequisiti

Monitoraggio di Azure supporta System Center Configuration Manager current branch, versione 1606 e successive.  

## <a name="configuration-overview"></a>Panoramica della configurazione
I passaggi seguenti riepilogano la procedura per configurare l'integrazione di Configuration Manager con monitoraggio di Azure.  

1. Nel portale di Azure registrare Configuration Manager come applicazione Web e/o app per le API Web e assicurarsi di avere la chiave privata e l'ID client dalla registrazione da Azure Active Directory. Vedere [Usare il portale per creare un'applicazione Active Directory e un'entità servizio che accedono alle risorse](../../active-directory/develop/howto-create-service-principal-portal.md) per informazioni dettagliate su come eseguire questo passaggio.
2. Nel portale di Azure [concedere a Configuration Manager (l'app web registrata) l'autorizzazione per accedere a monitoraggio di Azure](#grant-configuration-manager-with-permissions-to-log-analytics).
3. In Configuration Manager aggiungere una connessione tramite la procedura guidata di aggiunta di una connessione OMS.
4. In Configuration Manager aggiornare le proprietà di connessione se la chiave privata client o la password dovesse scadere o andare persa.
5. [Scaricare e installare Microsoft Monitoring Agent](#download-and-install-the-agent) nel computer che esegue il ruolo di sistema del sito del punto di connessione del servizio di Configuration Manager. L'agente invia i dati di Configuration Manager all'area di lavoro di Log Analitica in Monitoraggio di Azure.
6. In Monitoraggio di Azure [importare le raccolte da Configuration Manager](#import-collections) come gruppi di computer.
7. In Monitoraggio di Azure, visualizzare i dati da Configuration Manager come [gruppi di computer](computer-groups.md).

Altre informazioni sulla connessione tra Configuration Manager per il monitoraggio di Azure in [Sincronizza i dati da Configuration Manager all'area di lavoro di Microsoft Log Analitica](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Fornire a Configuration Manager le autorizzazioni per Log Analytics
Nella procedura seguente si concede il ruolo *Collaboratore* nell'area di lavoro Log Analytics all'applicazione AD e all'entità servizio creata in precedenza per Configuration Manager.  Se si dispone già di un'area di lavoro, vedere [creare un'area di lavoro in Monitoraggio di Azure](../../azure-monitor/learn/quick-create-workspace.md) prima di procedere.  In questo modo, si consente a Configuration Manager di eseguire l'autenticazione e connettersi all'area di lavoro Log Analytics.  

> [!NOTE]
> È necessario specificare le autorizzazioni dell'area di lavoro di Log Analitica per Configuration Manager. In caso contrario, viene visualizzato un messaggio di errore quando si usa la procedura guidata di configurazione in Configuration Manager.
>

1. Nel portale di Azure fare clic su **Tutti i servizi** nell'angolo superiore sinistro. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.
2. Nell'elenco di aree di lavoro di Log Analytics selezionare l'area di lavoro da modificare.
3. Nel riquadro a sinistra selezionare **Controllo di accesso (IAM)** .
4. Nella pagina Controllo di accesso (IAM) fare clic su **Aggiungi un'assegnazione di ruolo**. Viene visualizzato il riquadro **Aggiungi un'assegnazione di ruolo**.
5. Nel riquadro **Aggiungi un'assegnazione di ruolo**, nell'elenco a discesa **Ruolo** selezionare il ruolo **Collaboratore**.  
6. Nell'elenco a discesa **Assegna accesso a** selezionare l'applicazione di Configuration Manager creata in precedenza in AD e quindi fare clic su **OK**.  

## <a name="download-and-install-the-agent"></a>Scaricare e installare l'agente
Vedere l'articolo [i computer Windows di connettersi a monitoraggio di Azure in Azure](agent-windows.md) per comprendere i metodi disponibili per l'installazione di Microsoft Monitoring Agent nel computer che ospita il punto di connessione del servizio di Configuration Manager ruolo del sistema del sito.  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>Aggiungere una connessione di Log Analytics a Configuration Manager
Per aggiungere una connessione di Log Analytics, è necessario che l'ambiente di Configuration Manager abbia un [punto di connessione del servizio](https://technet.microsoft.com/library/mt627781.aspx) configurato per la modalità online.

1. Nell'area di lavoro **Amministrazione** di Configuration Manager, selezionare **Connettore OMS** Viene così avviata la **Procedura guidata di aggiunta di una connessione Log Analytics**. Selezionare **Avanti**.

   >[!NOTE]
   >OMS è ora nota come Analitica di Log che è una funzionalità di monitoraggio di Azure.
   
2. Nella schermata **Generale**, confermare di aver eseguito le azioni seguenti e di aver ottenuto i dettagli per ogni elemento, quindi selezionare **Avanti**.

   1. Nel portale di Azure, Configuration Manager è stato registrato come applicazione Web e/o app per le API Web ed è disponibile l'[ID client dalla registrazione](../../active-directory/develop/quickstart-register-app.md).
   2. Nel portale di Azure è stata creata una chiave privata per l'app registrata in Azure Active Directory.  
   3. Nel portale di Azure, l'app web registrata è stata concessa l'autorizzazione per accedere all'area di lavoro di Log Analitica in Monitoraggio di Azure.  
      ![Pagina generale della Procedura guidata di aggiunta di una connessione Log Analytics](./media/collect-sccm/sccm-console-general01.png)
3. Nel **Azure Active Directory** schermata, configurare le impostazioni di connessione per l'area di lavoro di Log Analitica, fornendo le **Tenant**, **ID Client**e **Chiave privata client**, quindi selezionare **successiva**.  
   ![Pagina di Azure Active Directory relativa alla Procedura guidata di aggiunta di una connessione Log Analytics](./media/collect-sccm/sccm-wizard-tenant-filled03.png)
4. Se tutte le altre procedure sono state portate a termine, allora questa pagina conterrà automaticamente le informazioni relative alla schermata di **configurazione della connessione OMS**. Dovrebbero essere visualizzate informazioni per le impostazioni di connessione in merito alla **sottoscrizione di Azure**, al **gruppo di risorse Azure** e **all'area di lavoro di Operations Management Suite**.  
   ![Pagina di connessione di Log Analytics della Procedura guidata di aggiunta di una connessione Log Analytics](./media/collect-sccm/sccm-wizard-configure04.png)
5. La procedura guidata si connette all'area di lavoro di Log Analitica usando le informazioni che immesse dall'utente. Selezionare le raccolte di dispositivi da sincronizzare con il servizio e quindi fare clic su **Aggiungi**.  
   ![Seleziona raccolte](./media/collect-sccm/sccm-wizard-add-collections05.png)
6. Verificare le impostazioni di connessione nella schermata **Riepilogo** e selezionare **Avanti**. La schermata **In corso** mostra lo stato della connessione, che successivamente diventerà **Completa**.

> [!NOTE]
> È necessario connettere il sito di livello superiore nella gerarchia di monitoraggio di Azure. Se si connette un sito primario autonomo a monitoraggio di Azure e quindi aggiungerla un sito di amministrazione centrale all'ambiente, è necessario eliminare e ricreare la connessione all'interno della nuova gerarchia.
>
>

Dopo aver collegato Configuration Manager a monitoraggio di Azure, è possibile aggiungere o rimuovere raccolte e visualizzare le proprietà della connessione.

## <a name="update-log-analytics-workspace-connection-properties"></a>Aggiornare le proprietà di connessione dell'area di lavoro di Log Analitica
In caso di scadenza o smarrimento della chiave privata del client o della password, è necessario aggiornare manualmente le proprietà di connessione di Log Analytics.

1. In Configuration Manager passare a **Servizi cloud** e quindi selezionare **Connettore OMS** per aprire la pagina **Proprietà di connessione OMS**.
2. In questa pagina, fare clic sulla scheda **Azure Active Directory** per visualizzare il **tenant**, l'**ID client** e la **scadenza della chiave privata client**. **Verificare** se la **chiave privata client** è scaduta.

## <a name="import-collections"></a>Importare le raccolte
Dopo aver aggiunto una connessione di Log Analitica per Configuration Manager e aver installato l'agente nel computer che esegue la connessione del servizio Configuration Manager punto ruolo del sistema del sito, il passaggio successivo consiste nell'importare le raccolte da Configuration Manager in Azure Monitorare gruppi di computer.

Dopo aver completato la configurazione iniziale per importare le raccolte di dispositivi dalla gerarchia, le informazioni sull'appartenenza alle raccolte vengono recuperate ogni 3 ore per mantenere aggiornati i dati relativi all'appartenenza. È possibile scegliere di disabilitare questa opzione in qualsiasi momento.

1. Nel portale di Azure fare clic su **Tutti i servizi** nell'angolo superiore sinistro. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.
2. Nell'elenco delle aree di lavoro di Log Analytics selezionare l'area di lavoro con cui è stato registrato Configuration Manager.  
3. Selezionare **Impostazioni avanzate**.
4. Selezionare **Gruppi di computer** e quindi **SCCM**.  
5. Selezionare **Importa appartenenze alla raccolta di Configuration Manager** e fare clic su **Salva**.  
   ![Gruppi di computer - Scheda SCCM](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Visualizzare i dati da Configuration Manager
Dopo aver aggiunto una connessione di Log Analitica per Configuration Manager e l'agente installato in computer che esegue il ruolo del sistema del sito Configuration Manager service connessione punto, i dati dall'agente vengono inviati all'area di lavoro di Log Analitica in Monitoraggio di Azure. In Monitoraggio di Azure, le raccolte di Configuration Manager vengono visualizzati come [gruppi di computer](../../azure-monitor/platform/computer-groups.md). È possibile visualizzare i gruppi dalla pagina **Configuration Manager** in **Impostazioni\Gruppi di computer**.

Una volta importate le raccolte, è possibile vedere quanti computer con appartenenze delle raccolte sono stati rilevati. È anche possibile visualizzare il numero di raccolte importate.

![Gruppi di computer - Scheda SCCM](./media/collect-sccm/sccm-computer-groups02.png)

Facendo clic su dei due, si apre la funzione di ricerca, nella quale vengono visualizzati tutti i gruppi importati o tutti i computer che appartengono a ciascun gruppo. Usando [Ricerca Log](../../azure-monitor/log-query/log-query-overview.md) è possibile avviare un'analisi approfondita dei dati di Configuration Manager.

## <a name="next-steps"></a>Passaggi successivi
* Usare [Ricerca Log](../../azure-monitor/log-query/log-query-overview.md) per visualizzare le informazioni dettagliate sui dati di Configuration Manager.
