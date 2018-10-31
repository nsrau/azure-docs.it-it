---
title: Connettere Configuration Manager a Log Analytics | Microsoft Docs
description: In questo articolo vengono illustrati i passaggi per connettere Configuration Manager a Log Analytics e avviare l'analisi dei dati.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 28ddfea0f4127f402b82388a10ee150b30a65736
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954233"
---
# <a name="connect-configuration-manager-to-log-analytics"></a>Connettere Configuration Manager a Log Analytics
È possibile connettere l'ambiente System Center Configuration Manager ad Azure Log Analytics per sincronizzare i dati delle raccolte di dispositivi e fare riferimento a queste raccolte in Log Analytics e Automazione di Azure.  

## <a name="prerequisites"></a>Prerequisiti

Log Analytics supporta System Center Configuration Manager Current Branch, versione 1606 e successive.  

## <a name="configuration-overview"></a>Panoramica della configurazione
La procedura seguente riepiloga i passaggi per configurare l'integrazione di Configuration Manager con Log Analytics.  

1. Nel portale di Azure registrare Configuration Manager come applicazione Web e/o app per le API Web e assicurarsi di avere la chiave privata e l'ID client dalla registrazione da Azure Active Directory. Vedere [Usare il portale per creare un'applicazione Active Directory e un'entità servizio che accedono alle risorse](../active-directory/develop/howto-create-service-principal-portal.md) per informazioni dettagliate su come eseguire questo passaggio.
2. Nel portale di Azure [concedere a Configuration Manager (l'app Web registrata) l'autorizzazione per l'accesso a Log Analytics](#grant-configuration-manager-with-permissions-to-log-analytics).
3. In Configuration Manager, [aggiungere una connessione tramite la procedura guidata di aggiunta di una connessione OMS](#add-an-oms-connection-to-configuration-manager).
4. In Configuration Manager [aggiornare le proprietà di connessione](#update-oms-connection-properties) se la chiave privata client o la password dovesse scadere o andare persa.
5. [Scaricare e installare Microsoft Monitoring Agent](#download-and-install-the-agent) nel computer che esegue il ruolo di sistema del sito del punto di connessione del servizio di Configuration Manager. L'agente invia i dati di Configuration Manager all'area di lavoro di Log Analytics.
6. In Log Analytics [importare le raccolte da Configuration Manager](#import-collections) come gruppi di computer.
7. In Log Analytics visualizzare i dati da Configuration Manager come [gruppi di computer](log-analytics-computer-groups.md).

Altre informazioni sulla connessione di Configuration Manager a Log Analytics sono disponibili nella sezione [Sincronizzare i dati da Configuration Manager a Microsoft Log Analytics](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Fornire a Configuration Manager le autorizzazioni per Log Analytics
Nella procedura seguente si concede il ruolo *Collaboratore* nell'area di lavoro di Log Analytics all'applicazione AD e all'entità servizio creata in precedenza per Configuration Manager.  Se non si ha già un'area di lavoro, vedere [Creare un'area di lavoro di Log Analytics nel portale di Azure](log-analytics-quick-create-workspace.md) prima di continuare.  In questo modo, si consente a Configuration Manager di eseguire l'autenticazione e connettersi all'area di lavoro di Log Analytics.  

> [!NOTE]
> È necessario specificare le autorizzazioni in Log Analytics per Configuration Manager. In caso contrario, viene visualizzato un messaggio di errore quando si usa la procedura guidata di configurazione in Configuration Manager.
>

1. Nel portale di Azure fare clic su **Tutti i servizi** nell'angolo superiore sinistro. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.<br><br> ![Portale di Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Nell'elenco di aree di lavoro di Log Analytics selezionare l'area di lavoro da modificare.
3. Nel riquadro a sinistra selezionare **Controllo di accesso (IAM)**.
4. Nella pagina Controllo di accesso fare clic su **Aggiungi** per visualizzare il riquadro **Aggiungi autorizzazioni**.
5. Nel riquadro **Aggiungi autorizzazioni**, nell'elenco a discesa **Ruolo** selezionare il ruolo **Collaboratore**.  
6. Nell'elenco a discesa **Assegna accesso a** selezionare l'applicazione di Configuration Manager creata in precedenza in AD e quindi fare clic su **OK**.  

## <a name="download-and-install-the-agent"></a>Scaricare e installare l'agente
Vedere l'articolo [Connettere computer Windows al servizio Log Analytics in Azure](log-analytics-agent-windows.md) per comprendere i metodi disponibili per l'installazione di Microsoft Monitoring Agent nel computer che ospita il ruolo di sistema del sito del punto di connessione del servizio di Configuration Manager.  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>Aggiungere una connessione di Log Analytics a Configuration Manager
Per aggiungere una connessione di Log Analytics, è necessario che l'ambiente di Configuration Manager abbia un [punto di connessione del servizio](https://technet.microsoft.com/library/mt627781.aspx) configurato per la modalità online.

1. Nell'area di lavoro **Amministrazione** di Configuration Manager, selezionare **Connettore OMS** Viene così avviata la **Procedura guidata di aggiunta di una connessione Log Analytics**. Selezionare **Avanti**.

   >[!NOTE]
   >OMS viene a questo punto indicato come Log Analytics.
   
2. Nella schermata **Generale**, confermare di aver eseguito le azioni seguenti e di aver ottenuto i dettagli per ogni elemento, quindi selezionare **Avanti**.

   1. Nel portale di Azure, Configuration Manager è stato registrato come applicazione Web e/o app per le API Web ed è disponibile l'[ID client dalla registrazione](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).
   2. Nel portale di Azure è stata creata una chiave privata per l'app registrata in Azure Active Directory.  
   3. Nel portale di Azure all'app Web registrata è stata concessa l'autorizzazione ad accedere a Log Analytics.  
      ![Pagina generale della Procedura guidata di aggiunta di una connessione Log Analytics](./media/log-analytics-sccm/sccm-console-general01.png)
3. Nella schermata **Azure Active Directory** configurare le impostazioni di connessione a Log Analytics fornendo **Tenant**, **ID client** e **Chiave privata del client** e quindi selezionare **Avanti**.  
   ![Pagina di Azure Active Directory relativa alla Procedura guidata di aggiunta di una connessione Log Analytics](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. Se tutte le altre procedure sono state portate a termine, allora questa pagina conterrà automaticamente le informazioni relative alla schermata di **configurazione della connessione OMS**. Dovrebbero essere visualizzate informazioni per le impostazioni di connessione in merito alla **sottoscrizione di Azure**, al **gruppo di risorse Azure** e **all'area di lavoro di Operations Management Suite**.  
   ![Pagina di connessione di Log Analytics della Procedura guidata di aggiunta di una connessione Log Analytics](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. La procedura guidata stabilisce la connessione al servizio Log Analytics usando le informazioni fornite. Selezionare le raccolte di dispositivi da sincronizzare con il servizio e quindi fare clic su **Aggiungi**.  
   ![Seleziona raccolte](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. Verificare le impostazioni di connessione nella schermata **Riepilogo** e selezionare **Avanti**. La schermata **In corso** mostra lo stato della connessione, che successivamente diventerà **Completa**.

> [!NOTE]
> È necessario connettere il sito di livello superiore nella gerarchia a Log Analytics. Se si connette un sito primario autonomo a Log Analytics e successivamente si aggiunge un sito di amministrazione centrale all'ambiente, è necessario eliminare e ricreare la connessione all'interno della nuova gerarchia.
>
>

Dopo aver collegato Configuration Manager a Log Analytics, è possibile aggiungere o rimuovere le raccolte e visualizzare le proprietà della connessione.

## <a name="update-log-analytics-connection-properties"></a>Aggiornare le proprietà di connessione di Log Analytics
In caso di scadenza o smarrimento della chiave privata del client o della password, è necessario aggiornare manualmente le proprietà di connessione di Log Analytics.

1. In Configuration Manager passare a **Servizi cloud** e quindi selezionare **Connettore OMS** per aprire la pagina **Proprietà di connessione OMS**.
2. In questa pagina, fare clic sulla scheda **Azure Active Directory** per visualizzare il **tenant**, l'**ID client** e la **scadenza della chiave privata client**. **Verificare** se la **chiave privata client** è scaduta.

## <a name="import-collections"></a>Importare le raccolte
Dopo avere aggiunto una connessione di Log Analytics a Configuration Manager e avere installato l'agente nel computer che esegue il ruolo di sistema del sito del punto di connessione del servizio di Configuration Manager, il passaggio successivo consiste nell'importare le raccolte da Configuration Manager a Log Analytics come gruppi di computer.

Dopo aver completato la configurazione iniziale per importare le raccolte di dispositivi dalla gerarchia, le informazioni sull'appartenenza alle raccolte vengono recuperate ogni 3 ore per mantenere aggiornati i dati relativi all'appartenenza. È possibile scegliere di disabilitare questa opzione in qualsiasi momento.

1. Nel portale di Azure fare clic su **Tutti i servizi** nell'angolo superiore sinistro. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.
2. Nell'elenco delle aree di lavoro di Log Analytics selezionare l'area di lavoro con cui è stato registrato Configuration Manager.  
3. Selezionare **Impostazioni avanzate**.<br><br> ![Impostazioni avanzate di Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Selezionare **Gruppi di computer** e quindi **SCCM**.  
5. Selezionare **Importa appartenenze alla raccolta di Configuration Manager** e fare clic su **Salva**.  
   ![Gruppi di computer - Scheda SCCM](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Visualizzare i dati da Configuration Manager
Dopo aver aggiunto una connessione di Log Analytics a Configuration Manager e aver installato l'agente nel computer che esegue il ruolo di sistema del sito del punto di connessione del servizio di Configuration Manager, i dati provenienti dall'agente vengono inviati a Log Analytics. In Log Analytics le raccolte di Configuration Manager vengono visualizzate come [gruppi di computer](log-analytics-computer-groups.md). È possibile visualizzare i gruppi dalla pagina **Configuration Manager** in **Impostazioni\Gruppi di computer**.

Una volta importate le raccolte, è possibile vedere quanti computer con appartenenze delle raccolte sono stati rilevati. È anche possibile visualizzare il numero di raccolte importate.

![Gruppi di computer - Scheda SCCM](./media/log-analytics-sccm/sccm-computer-groups02.png)

Facendo clic su dei due, si apre la funzione di ricerca, nella quale vengono visualizzati tutti i gruppi importati o tutti i computer che appartengono a ciascun gruppo. Usando [Ricerca Log](log-analytics-log-searches.md) è possibile avviare un'analisi approfondita dei dati di Configuration Manager.

## <a name="next-steps"></a>Passaggi successivi
* Usare [Ricerca Log](log-analytics-log-searches.md) per visualizzare le informazioni dettagliate sui dati di Configuration Manager.
