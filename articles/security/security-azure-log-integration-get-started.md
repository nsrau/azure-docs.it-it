---
title: Introduzione all&quot;integrazione dei log di Azure | Documentazione Microsoft
description: Informazioni su come installare il servizio di integrazione dei log di Azure e integrare i log dall&quot;archiviazione di Azure, i log di controllo di Azure e gli avvisi del Centro sicurezza di Azure.
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 04/07/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 2752ae92fbbbb284756215a53dcab054881bd08a
ms.lasthandoff: 04/13/2017


---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Integrazione dei log di Azure con la registrazione di Diagnostica di Azure e l'inoltro di eventi di Windows
Integrazione log di Azure (AzLog) consente di integrare log non elaborati delle risorse di Azure nei sistemi di gestione di informazioni ed eventi di sicurezza locali. Questa integrazione consente di avere un dashboard di sicurezza unificato per tutti gli asset, locali o su cloud, consentendo di aggregare, correlare, analizzare e inviare avvisi per gli eventi di sicurezza associati alle applicazioni.
>[!NOTE]
Per altre informazioni su Integrazione dei log di Azure, vedere la [panoramica di Integrazione log di Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-overview).

Questo articolo presenta un'introduzione a Integrazione log di Azure concentrandosi sull'installazione del servizio Azlog e l'integrazione del servizio con Diagnostica di Azure. Il servizio Integrazione log di Azure sarà quindi in grado di raccogliere informazioni del registro eventi di Windows dal canale degli eventi di sicurezza di Windows da macchine virtuali distribuite in Azure IaaS. Questo è molto simile all'inoltro degli eventi che si può usare in locale.

>[!NOTE]
>La possibilità di portare l'output dell'integrazione dei log di Azure nel SIEM viene fornita dal SIEM stesso. Per altre informazioni vedere l'articolo [Azure Log Integration SIEM configuration steps](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) (Passaggi per la configurazione di SIEM con Integrazione log di Azure).

Deve essere chiaro che il servizio Integrazione log di Azure viene eseguito in un computer fisico o virtuale dotato del sistema operativo Windows Server 2008 R2 o versioni successive (le versioni preferite sono Windows Server 2012 R2 o Windows Server 2016). 

Il computer fisico può essere eseguito in locale (o in un sito host). Se si sceglie di eseguire il servizio Integrazione log di Azure in una macchina virtuale, tale macchina virtuale può essere in locale o in un cloud pubblico, come Microsoft Azure. 

La macchina fisica o virtuale che esegue il servizio Integrazione log di Azure richiede la connettività di rete per il cloud pubblico di Azure. I passaggi riportai in questo articolo forniscono i dettagli della configurazione.

## <a name="prerequisites"></a>Prerequisiti
Come minimo l'installazione di AzLog richiede i seguenti elementi:
* Una **sottoscrizione di Azure**. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Un **account di archiviazione** che può essere usato per la registrazione diagnostica di Windows Azure (è possibile usare un account di archiviazione preconfigurato o crearne uno nuovo: più avanti in questo articolo è descritto come configurare l'account di archiviazione)
* **Due sistemi**: un computer in cui verrà eseguito il servizio Integrazione log di Azure e un computer che verrà monitorato e le sue informazioni di registrazione saranno inviate al computer del servizio Azlog.
   * Un computer che si desidera monitorare: si tratta di una VM eseguita come [macchina virtuale di Azure](../virtual-machines/virtual-machines-windows-overview.md)
   * Un computer in cui verrà eseguito il servizio di integrazione dei log di Azure; questo computer raccoglierà tutte le informazioni dei log che verranno successivamente importate nel SIEM.
    * Questo sistema può essere locale o in Microsoft Azure.  
    * Deve eseguire un versione x64 di Windows Server 2008 R2 SP1 o versione successiva e avere installato .NET 4.5.1. È possibile determinare la versione di .NET installata seguendo l'articolo intitolato [Procedura: determinare le versioni di .NET Framework installate](https://msdn.microsoft.com/library/hh925568)  
    Deve disporre di connettività all'account di archiviazione di Azure utilizzato per la registrazione diagnostica di Azure. Microsoft fornirà le istruzioni su come verificare la connettività più avanti in questo articolo

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione
Durante il test di Integrazione log di Azure è possibile usare qualsiasi sistema che soddisfi i requisiti minimi del sistema operativo. Tuttavia, per un ambiente di produzione, il carico potrebbe richiedere una pianificazione per aumentare o scalare orizzontalmente.

Se il volume di eventi è elevato, è possibile eseguire più istanze del servizio Integrazione log di Azure (un'istanza per ogni macchina virtuale o fisica). Inoltre è possibile eseguire il bilanciamento del carico degli account di archiviazione di Diagnostica di Azure per Windows (WAD) e il numero di sottoscrizioni da fornire alle istanze deve essere basato sulla capacità.
>[!NOTE]
In questo momento non abbiamo consigli specifici su quando scalare orizzontalmente le istanze dei computer con l'integrazione dei log di Azure (ovvero i computer che eseguono il servizio di integrazione dei log di Azure) o per le sottoscrizioni o gli account di archiviazione. Le decisioni di scalabilità si devono basare sulle osservazioni delle prestazioni in ognuna di queste aree.

È inoltre possibile aumentare il servizio Integrazione log di Azure per migliorare le prestazioni. Le seguenti metriche di prestazioni consentono di dimensionare i computer scelti per l'esecuzione del servizio di integrazione dei log di Azure:
* Su un computer con 8 processori (memoria centrale), una singola istanza dell'integratore Azlog può elaborare circa 24 milioni di eventi al giorno (all'incirca 1 milione all'ora).

* Su un computer con 4 processori (memoria centrale), una singola istanza dell'integratore Azlog può elaborare circa 1,5 milioni di eventi al giorno (all'incirca 62,5 K all'ora).

## <a name="install-azure-log-integration"></a>Installare l'integrazione dei log di Azure
Per installare Integrazione log di Azure è necessario scaricare il file di installazione [Integrazione dei log di Azure](https://www.microsoft.com/download/details.aspx?id=53324). Eseguire la routine di installazione e decidere se si desidera fornire informazioni di telemetria a Microsoft.  

![Schermata di installazione con la casella della telemetria selezionata](./media/security-azure-log-integration-get-started/telemetry.png)

*
> [!NOTE]
> Si consiglia di consentire a Microsoft di raccogliere i dati di telemetria. Per disabilitare la raccolta dei dati di telemetria è possibile deselezionare questa opzione.
>


Il servizio Integrazione log di Azure raccoglie i dati di telemetria dal computer in cui è installato.  

I dati di telemetria raccolti sono:

* Eccezioni che si verificano durante l'esecuzione dell'integrazione dei log di Azure
* Metriche relative al numero di query e di eventi elaborati
* Statistiche sulle opzioni della riga di comando Azlog.exe che vengono usate


## <a name="post-installation-and-validation-steps"></a>Passaggi successivi all'installazione e di convalida
Dopo aver completato la routine di installazione di base si può procedere con i passaggi successivi all'installazione e di convalida:
1. Aprire una finestra di PowerShell con privilegi elevati e passare a **C:\Programmi\Integrazione log di Microsoft Azure**
2. Il primo passaggio consiste nell'ottenere i cmdlet AzLog importati. È possibile farlo eseguendo lo script**LoadAzlogModule.ps1** (notare i caratteri ". \" nel comando seguente). Digitare **.\LoadAzlogModule.ps1** e premere **INVIO**.  
Verrà visualizzata una schermata simile alla figura seguente. </br></br>
![Schermata di installazione con la casella della telemetria selezionata](./media/security-azure-log-integration-get-started/loaded-modules.png) </br></br>
3. A questo punto è necessario configurare AzLog per l'uso di un ambiente di Azure specifico. "Ambiente di Azure" è il "tipo" di data center del cloud di Azure che si desidera usare. Sebbene esistano diversi ambienti di Azure in questa fase, le opzioni attualmente pertinenti sono **AzureCloud** o **AzureUSGovernment**.   Nell'ambiente di PowerShell con privilegi elevati assicurarsi di essere in **C:\Programmi\Integrazione log di Microsoft Azure\** </br></br>
    Da questa posizione eseguire il comando: </br>
    ``Set-AzlogAzureEnvironment -Name AzureCloud`` (per l'area commerciale di Azure)

      >[!NOTE]
      Quando il comando ha esito positivo, non si riceveranno commenti.  Se si desidera usare il cloud del governo USA di Microsoft Azure occorre utilizzare **AzureUSGovernment** (per la variabile -Name) per il cloud del governo USA. Al momento gli altri cloud di Azure non sono supportati.  
4. Per poter monitorare un sistema è necessario il nome dell'account di archiviazione in uso per Diagnostica di Azure.  Nel portale di Azure passare a **Macchine virtuali** e cercare la macchina virtuale che verrà monitorata. Nella sezione **Proprietà** scegliere **Impostazioni di diagnostica**.  Fare clic su **Agente** e prendere nota del nome dell'account di archiviazione specificato. Il nome dell'account sarà necessario per un passaggio successivo.
![Impostazioni di Diagnostica di Azure](./media/security-azure-log-integration-get-started/storage-account-large.png) </br></br>

      ![Impostazioni di Diagnostica di Azure](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)
      >[!NOTE]
      Se il monitoraggio non è stato abilitato durante la creazione della macchina virtuale sarà possibile abilitarlo come illustrato in precedenza. 
5. Ora si tornerà al computer con l'integrazione dei log di Azure. È necessario verificare di disporre della connettività all'account di archiviazione dal sistema in cui è stato installato Integrazione log di Azure. Il computer fisico o la macchina virtuale che esegue il servizio Integrazione log di Azure deve accedere all'account di archiviazione per recuperare le informazioni registrate da Diagnostica di Azure configurate in ciascuno dei sistemi monitorati.  
  1. È possibile scaricare Azure Storage Explorer [qui](http://storageexplorer.com/).
  2. Eseguire la routine di installazione
  3. Una volta completata l'installazione fare clic su **Avanti** e lasciare selezionata la casella di controllo **Avviare Esplora archivi di Microsoft Azure**.  
  4. Accedere ad Azure.
  5. Verificare che sia possibile vedere l'account di archiviazione configurato per la Diagnostica di Azure.  
![Account di archiviazione](./media/security-azure-log-integration-get-started/storage-account.jpg) </br></br>
   6. Si noti che ci sono alcune opzioni sotto gli account di archiviazione. Una di esse è **Tabelle**. Sotto **Tabelle** dovrebbe essere presente una tabella denominata **WADWindowsEventLogsTable**. </br></br>
   ![Account di archiviazione](./media/security-azure-log-integration-get-started/storage-explorer.png) </br>

## <a name="integrate-azure-diagnostic-logging"></a>Integrare la registrazione di Diagnostica di Azure
In questo passaggio si configurerà il computer che esegue il servizio Integrazione log di Azure per la connessione all'account di archiviazione che contiene i file di log.
Per poter completare questo passaggio sarà necessario disporre di alcune cose.  
* **FriendlyNameForSource:** è un nome descrittivo che è possibile applicare all'account di archiviazione configurato per l'archiviazione delle informazioni di Diagnostica di Azure da parte della macchina virtuale
* **StorageAccountName:** questo è il nome dell'account di archiviazione specificato durante la configurazione di Diagnostica di Azure.  
* **Chiave di archiviazione:** si tratta della chiave di archiviazione per l'account di archiviazione in cui le informazioni di Diagnostica di Azure vengono archiviate per questa macchina virtuale.  

Eseguire i passaggi seguenti per ottenere la chiave di archiviazione:
 1. Accedere al [portale di Azure](http://portal.azure.com).
 2. Nel riquadro di spostamento della console di Azure scorrere verso il basso e fare clic su **Altri servizi**.

 ![Altri servizi](./media/security-azure-log-integration-get-started/more-services.png)
 3. Immettere **Archiviazione** nella casella di testo **Filtro**. Fare clic su **Account di archiviazione** (il valore sarà visualizzato dopo avere immesso **Archiviazione**)

  ![casella Filtro](./media/security-azure-log-integration-get-started/filter.png)
 4. Verrà visualizzato un elenco di account di archiviazione: fare doppio clic sull'account assegnato all'archiviazione dei log.

   ![elenco degli account di archiviazione](./media/security-azure-log-integration-get-started/storage-accounts.png)
 5. Fare clic su **Chiavi di accesso** nella sezione **Impostazioni**.

  ![chiavi di accesso](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 6. Copiare **key1** e inserirla in una posizione sicura a cui sia possibile accedere per il passaggio successivo.

   ![due chiavi di accesso](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 7. Nel server in cui è stato installato Integrazione log di Azure, aprire un prompt dei comandi con privilegi elevati (si noti che in questo caso verrà usata una finestra del prompt dei comandi con privilegi elevati e non una console di PowerShell con privilegi elevati).
 8. Passare alla directory **C:\Programmi\Integrazione log di Microsoft Azure**
 9. Eseguire ``Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey> `` </br> Ad esempio ``Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`` se si vuole che l'ID sottoscrizione venga visualizzato nell'XML dell'evento, aggiungere l'ID sottoscrizione al nome descrittivo: ``Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`` o ad esempio ``Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==``

>[!NOTE]  
Attendere 60 minuti quindi visualizzare gli eventi che vengono estratti dall'account di archiviazione. Per visualizzarli, aprire **Visualizzatore eventi > Registri di Windows > Eventi inoltrati** nell'integratore Azlog.

## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>Se i dati non vengono visualizzati nella cartella Eventi inoltrati?
Se, dopo un'ora, i dati non vengono visualizzati nella cartella **Eventi inoltrati** , procedere come segue:

1. Controllare il computer che esegue il servizio Integrazione log di Azure e verificare che possa accedere ad Azure. Per verificare la connettività, provare ad aprire il [portale di Azure](http://portal.azure.com) dal browser.
2. Verificare che l'account utente **Azlog** abbia l'autorizzazione di scrittura per la cartella **users\Azlog**.
  <ol type="a">
   <li>Aprire **Esplora risorse** </li>
  <li> Passare a **C:\users** </li>
  <li> Fare clic con il pulsante destro del mouse su **C:\users\Azlog** </li>
  <li> Fare clic su **Protezione**  </li>
  <li> Fare clic su **Servizio NT\Azlog** e controllare le autorizzazioni per l'account. Se l'account non è presente in questa scheda o se le autorizzazioni appropriate non sono attualmente visualizzate è possibile assegnare diritti all'account in questa scheda.</li>
  </ol>
3. Verificare che l'account di archiviazione aggiunto nel comando **Azlog source add** sia elencato quando si esegue il comando **Azlog source list**.
4. Passare a **Visualizzatore eventi > Registri di Windows > Applicazione** per vedere se sono presenti errori segnalati dall'integrazione del log di Azure.

Se si verificano problemi durante l'installazione e la configurazione, aprire una [richiesta di supporto](../azure-supportability/how-to-create-azure-support-request.md) e selezionare **Integrazione log** come servizio per cui si richiede il supporto.

Un'altra possibilità per ottenere assistenza è il [forum MSDN su Integrazione log di Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Qui i membri della community possono aiutarsi reciprocamente con domande, risposte, suggerimenti e trucchi su come ottenere il massimo da Integrazione log di Azure. Inoltre, il team di Integrazione log di Azure monitora questo forum e offrirà il suo contributo quando possibile.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Integrazione log di Azure, vedere i documenti seguenti:

* [Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324) (Integrazione log di Microsoft Azure) - Area download per informazioni dettagliate, requisiti di sistema e istruzioni di installazione per l'integrazione dei log di Azure.
* [Introduzione all'integrazione dei log di Azure](security-azure-log-integration-overview.md) – Questo documento presenta l'integrazione dei log di Azure, le funzionalità chiave e il funzionamento.
* [Passaggi per la configurazione dei partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – Questo post di blog mostra come configurare l'integrazione dei log di Azure per lavorare con le soluzioni partner Splunk, HP ArcSight e IBM QRadar.
* [Domande frequenti sull'integrazione dei log di Azure](security-azure-log-integration-faq.md) - Queste domande frequenti riguardano l'integrazione dei log di Azure.
* [Integrazione degli avvisi del Centro sicurezza con l'integrazione dei log di Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) - Questo documento mostra come sincronizzare gli avvisi del Centro sicurezza di Azure, insieme agli eventi di sicurezza delle macchine virtuali raccolti da Diagnostica di Azure e dai log di controllo di Azure, con la propria soluzione SIEM o di analisi dei log.
* [Nuove funzionalità per Diagnostica di Azure e i log di controllo di Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) - Questo post di blog presenta i log di controllo di Azure e altre funzionalità che consentono di ottenere informazioni dettagliate sulle operazioni delle risorse di Azure.

