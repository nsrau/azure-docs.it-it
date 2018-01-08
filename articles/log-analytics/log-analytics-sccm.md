---
title: Connettere Configuration Manager a Log Analytics | Documentazione Microsoft
description: In questo articolo vengono illustrati i passaggi per connettere Configuration Manager a Log Analytics e avviare l'analisi dei dati.
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: banders
ms.openlocfilehash: 7acf0cbd4f4cba885e6cc91dfe3cb68306a3649a
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="connect-configuration-manager-to-log-analytics"></a>Connettere Configuration Manager a Log Analytics
È possibile connettere System Center Configuration Manager a Log Analytics in OMS per sincronizzare i dati della raccolta dei dispositivi. Ciò rende disponibili in OMS i dati provenienti dalla gerarchia di Configuration Manager.

## <a name="prerequisites"></a>Prerequisiti

Log Analytics supporta System Center Configuration Manager Current Branch, versione 1606 e successive.  

## <a name="configuration-overview"></a>Panoramica della configurazione
I passaggi seguenti riepilogano il processo per connettere Analitica di Log di Configuration Manager.  

1. Nel portale di Azure, la registrazione di Configuration Manager come un'applicazione Web e/o API Web di app e assicurarsi di disporre l'ID client e chiave privata del client dalla registrazione da Azure Active Directory. Vedere [portale utilizzare per creare un'entità di servizi e applicazione che può accedere alle risorse di Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) per informazioni dettagliate su come eseguire questo passaggio.
2. Nel portale di Azure, [fornire Gestione configurazione (app web registrati) con l'autorizzazione di accedere a OMS](#provide-configuration-manager-with-permissions-to-oms).
3. In Configuration Manager, [aggiungere una connessione tramite la procedura guidata di aggiunta di una connessione OMS](#add-an-oms-connection-to-configuration-manager).
4. In Configuration Manager [aggiornare le proprietà di connessione](#update-oms-connection-properties) se la chiave privata client o la password dovesse scadere o andare persa.
5. Con le informazioni prese dal portale di OMS, [scaricare e installare Microsoft Monitoring Agent](#download-and-install-the-agent) sul computer che esegue il ruolo di sistema del sito del punto di connessione del servizio di Configuration Manager. L'agente invia i dati di Configuration Manager a OMS.
6. In Log Analytics [importare le raccolte da Configuration Manager](#import-collections) come gruppi di computer.
7. In Log Analytics visualizzare i dati da Configuration Manager come [gruppi di computer](log-analytics-computer-groups.md).

Altre informazioni sulla connessione di Configuration Manager a OMS sono disponibili alla sezione [Sincronizzare i dati da Configuration Manager a Microsoft Operations Management Suite](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="provide-configuration-manager-with-permissions-to-oms"></a>Fornire a Configuration Manager le autorizzazioni per accedere a OMS
La procedura seguente fornisce il portale di Azure con le autorizzazioni per accedere a OMS. In particolare, è necessario concedere il *ruolo Collaboratore* agli utenti nel gruppo di risorse per consentire al portale di Azure per la connessione di Configuration Manager a OMS.

> [!NOTE]
> È necessario specificare le autorizzazioni di accesso a OMS per Configuration Manager. In caso contrario, viene visualizzato un messaggio di errore quando si utilizza la configurazione guidata in Configuration Manager.
>
>

1. Aprire il [portale di Azure](https://portal.azure.com/) e fare clic su **Sfoglia** > **Analitica Log (OMS)** aprire Analitica Log (OMS).  
2. In **Analitica Log (OMS)**, fare clic su **Aggiungi** per aprire **area di lavoro OMS**.  
   ![OMS](./media/log-analytics-sccm/sccm-azure01.png)
3. In **area di lavoro OMS**, fornire le informazioni seguenti e quindi fare clic su **OK**.

   * **Area di lavoro di OMS**
   * **Sottoscrizione**
   * **Gruppo di risorse**
   * **Posizione**
   * **Piano tariffario**  
     ![OMS](./media/log-analytics-sccm/sccm-azure02.png)  

     > [!NOTE]
     > Nell'esempio sopra viene creato un nuovo gruppo di risorse. Il gruppo di risorse viene usato solo per fornire a Configuration Manager le autorizzazioni per l'area di lavoro di OMS in questo esempio.
     >
     >
4. Fare clic su **Sfoglia** > **gruppi di risorse** per aprire **gruppi di risorse**.
5. In **gruppi di risorse**, fare clic sul gruppo di risorse appena creata per aprire la &lt;nome gruppo di risorse&gt; impostazioni.  
   ![impostazioni di gruppo di risorse](./media/log-analytics-sccm/sccm-azure03.png)
6. In &lt;nome gruppo di risorse&gt; impostazioni, scegliere il controllo di accesso (IAM) per aprire &lt;nome gruppo di risorse&gt; gli utenti.  
   ![gruppo di risorse degli utenti](./media/log-analytics-sccm/sccm-azure04.png)  
7. In &lt;nome gruppo di risorse&gt; gli utenti, fare clic su **Aggiungi** per aprire **aggiungere accesso**.
8. In **aggiungere accesso**, fare clic su **selezionare un ruolo**, quindi selezionare il **collaboratore** ruolo.  
   ![selezionare un ruolo](./media/log-analytics-sccm/sccm-azure05.png)  
9. Fare clic su **Aggiungi utenti**, selezionare l'utente di Configuration Manager, fare clic su **Seleziona** e su **OK**.  
   ![aggiungi utenti](./media/log-analytics-sccm/sccm-azure06.png)  

## <a name="add-an-oms-connection-to-configuration-manager"></a>Aggiungere una connessione OMS a Configuration Manager
Per aggiungere una connessione OMS, è necessario che l'ambiente di Configuration Manager abbia un [punto di connessione del servizio](https://technet.microsoft.com/library/mt627781.aspx) configurato per la modalità online.

1. Nell'area di lavoro **Amministrazione** di Configuration Manager, selezionare **Connettore OMS** per aprire la **Procedura guidata di aggiunta di una connessione OMS**. Selezionare **Avanti**.
2. Nella schermata **Generale**, confermare di aver eseguito le azioni seguenti e di aver ottenuto i dettagli per ogni elemento, quindi selezionare **Avanti**.

   1. Registrato nel portale di Azure, Configuration Manager come applicazione Web e/o API Web app e che si disponga di [ID client dalla registrazione](../active-directory/active-directory-integrating-applications.md).
   2. Nel portale di Azure, è stata creata una chiave privata dell'app per l'app registrato in Azure Active Directory.  
   3. Nel portale di Azure, aver fornito l'app web registrati con l'autorizzazione per accedere a OMS.  
      ![Pagina generale della procedura guidata di connessione a OMS](./media/log-analytics-sccm/sccm-console-general01.png)
3. Nel **Azure Active Directory** schermata, configurare le impostazioni di connessione a OMS, fornendo il **Tenant**, **ID Client**, e **chiave privata Client** , quindi selezionare **Avanti**.  
   ![Pagina di Azure Active Directory relativa alla procedura guidata di connessione a OMS](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. Se tutte le altre procedure sono state portate a termine, allora questa pagina conterrà automaticamente le informazioni relative alla schermata di **configurazione della connessione OMS**. Dovrebbero essere visualizzate informazioni per le impostazioni di connessione per il **sottoscrizione di Azure**, **gruppo di risorse**, e **area di lavoro di Operations Management Suite**.  
   ![Pagina della procedura guidata di connessione a OMS](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. La procedura guidata stabilisce la connessione al servizio OMS usando le informazioni fornite. Selezionare le raccolte di dispositivi che si desidera sincronizzare con OMS e fare clic su **Aggiungi**.  
   ![Seleziona raccolte](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. Verificare le impostazioni di connessione nella schermata **Riepilogo** e selezionare **Avanti**. La schermata **In corso** mostra lo stato della connessione, che successivamente diventerà **Completa**.

> [!NOTE]
> È necessario connettere OMS al sito di livello superiore nella gerarchia. Se si connette OMS a un sito primario autonomo e quindi aggiunta un sito di amministrazione centrale all'ambiente, è necessario eliminare e ricreare la connessione di OMS nella nuova gerarchia.
>
>

Dopo aver collegato Configuration Manager a OMS, è possibile aggiungere o rimuovere le raccolte e visualizzare le proprietà della connessione OMS.

## <a name="update-oms-connection-properties"></a>Aggiornare le proprietà della connessione OMS
In caso di scadenza o smarrimento della chiave privata client o della password, le proprietà della connessione OMS devono essere aggiornate manualmente.

1. In Configuration Manager, passare a **servizi Cloud**, quindi selezionare **OMS Connector** per aprire la **le proprietà di connessione di OMS** pagina.
2. In questa pagina, fare clic sulla scheda **Azure Active Directory** per visualizzare il **tenant**, l'**ID client** e la **scadenza della chiave privata client**. **Verificare** se la **chiave privata client** è scaduta.

## <a name="download-and-install-the-agent"></a>Scaricare e installare l'agente
1. Nel portale di OMS, [scaricare il file di installazione dell'agente da OMS](log-analytics-windows-agent.md).
2. Usare uno dei metodi seguenti per installare e configurare l'agente sul computer che esegue il ruolo di sistema del sito di connessione del servizio di Configuration Manager.
   * [Installare gli agenti con il programma di installazione](log-analytics-windows-agent.md)
   * [Installare l'agente usando la riga di comando](log-analytics-windows-agent.md)
   * [Installare l'agente usando DSC in Automazione di Azure](log-analytics-windows-agent.md)

## <a name="import-collections"></a>Importare le raccolte
Dopo aver aggiunto una connessione OMS a Configuration Manager e aver installato l'agente sul computer che esegue il ruolo di sistema del sito di connessione del servizio di Configuration Manager, il passaggio successivo consiste nell'importare le raccolte da Configuration Manager in OMS come gruppi di computer.

Dopo aver abilitato l'importazione, le informazioni di appartenenza delle raccolte vengono recuperate ogni 3 ore per mantenere le appartenenze delle raccolte correnti. È possibile scegliere di disabilitare l'importazione in qualsiasi momento.

1. Nel portale di OMS, fare clic su **Impostazioni**.
2. Fare clic sulla scheda **Gruppi di computer** e selezionare la scheda **SCCM**.
3. Selezionare **Importa appartenenze alla raccolta di Configuration Manager** e fare clic su **Salva**.  
   ![Gruppi di computer - Scheda SCCM](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Visualizzare i dati da Configuration Manager
Dopo aver aggiunto una connessione OMS a Configuration Manager e aver installato l'agente sul computer che esegue il ruolo di sistema del sito di connessione del servizio di Configuration Manager, i dati provenienti dall'agente vengono inviati a OMS. In OMS, le raccolte di Configuration Manager appaiono come [gruppi di computer](log-analytics-computer-groups.md). È possibile visualizzare i gruppi dalla pagina **Configuration Manager** sotto a **Gruppi di Computer** in **Impostazioni**.

Una volta importate le raccolte, è possibile vedere quanti computer con appartenenze delle raccolte sono stati rilevati. È anche possibile visualizzare il numero di raccolte importate.

![Gruppi di computer - Scheda SCCM](./media/log-analytics-sccm/sccm-computer-groups02.png)

Facendo clic su dei due, si apre la funzione di ricerca, nella quale vengono visualizzati tutti i gruppi importati o tutti i computer che appartengono a ciascun gruppo. Usando [Ricerca Log](log-analytics-log-searches.md) è possibile avviare un'analisi approfondita dei dati di Configuration Manager.

## <a name="next-steps"></a>Passaggi successivi
* Usare [Ricerca Log](log-analytics-log-searches.md) per visualizzare le informazioni dettagliate sui dati di Configuration Manager.
