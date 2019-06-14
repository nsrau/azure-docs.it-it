---
title: Introduzione ad Integrazione log di Azure | Microsoft Docs
description: Informazioni su come installare il servizio Integrazione log di Azure e integrare i log da Archiviazione di Azure, i log di controllo di Azure e gli avvisi del Centro sicurezza di Azure.
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: b1df35c82ffb8b98f8b7bf86c21694b62b3804e4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66298291"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Integrazione log di Azure con la registrazione di Diagnostica di Azure e inoltro di eventi di Windows


>[!IMPORTANT]
> La funzionalità di integrazione Log di Azure verrà deprecata entro 15/06/2019. Il download di AzLog è stato disabilitato il 27 giugno 2018. Per materiale sussidiario su cosa fare dopo, vedere il post [Use Azure monitor to integrate with SIEM tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) (Usare Monitoraggio di Azure per eseguire l'integrazione con gli strumenti per le informazioni di sicurezza e gestione degli eventi) 

Usare Integrazione log di Azure se un connettore di [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-get-started.md) non è disponibile presso il fornitore di informazioni di sicurezza e gestione degli eventi.

Integrazione log di Azure rende disponibili i log di Azure al sistema SIEM in modo che sia possibile creare un dashboard di sicurezza unificato per tutti gli asset.
Per altre informazioni sullo stato di un connettore di Monitoraggio di Azure, contattare il fornitore SIEM.

> [!IMPORTANT]
> Se l'obiettivo principale è la raccolta di log di macchine virtuali, tenere presente che la maggior parte dei fornitori SIEM include questa opzione nella soluzione offerta. L'uso del connettore del fornitore SIEM è sempre l'opzione preferita.

Questo articolo illustra come iniziare a usare Integrazione log di Azure. Descrive principalmente l'installazione del servizio Integrazione Log di Azure e l'integrazione del servizio con Diagnostica di Azure. Il servizio Integrazione log di Azure raccoglie quindi informazioni del registro eventi di Windows dal canale degli eventi di sicurezza di Windows da macchine virtuali distribuite in un'infrastruttura distribuita come servizio di Azure. Questa funzionalità è simile all'*inoltro di eventi* che è possibile usare in un sistema locale.

> [!NOTE]
> L'integrazione dell'output di Integrazione log di Azure con un sistema SIEM viene eseguita dal sistema SIEM stesso. Per altre informazioni, vedere [Integrating Azure Log Integration with your On-premises SIEM](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) (Integrazione del servizio Integrazione log di Azure con il sistema SIEM locale).

Il servizio Integrazione log di Azure viene eseguito in un computer fisico o virtuale che esegue Windows Server 2008 R2 o versione successiva. È preferibile Windows Server 2016 o Windows Server 2012 R2.

Un computer fisico può essere eseguito in locale o in un sito host. Se si sceglie di eseguire il servizio Integrazione log di Azure in una macchina virtuale, la macchina virtuale può essere in locale o in un cloud pubblico, ad esempio in Microsoft Azure.

La macchina fisica o virtuale che esegue il servizio Integrazione log di Azure richiede la connettività di rete per il cloud pubblico di Azure. Questo articolo fornisce informazioni dettagliate sulla configurazione necessaria.

## <a name="prerequisites"></a>Prerequisiti

Per installare Integrazione log di Azure, sono necessari almeno gli elementi seguenti:

* Una **sottoscrizione di Azure**. Se non si ha un account, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Un **account di archiviazione** che può essere usato per la registrazione di Diagnostica di Microsoft Azure (WAD, Windows Azure Diagnostics). È possibile usare un account di archiviazione preconfigurato o crearne uno nuovo. Più avanti in questo articolo viene descritto come configurare l'account di archiviazione.

  > [!NOTE]
  > A seconda dello scenario, potrebbe non essere necessario un account di archiviazione. Per lo scenario di Diagnostica di Azure illustrato in questo articolo è necessario un account di archiviazione.

* **Due sistemi**: 
  * Un computer che esegue il servizio Integrazione di log di Azure. Questo computer raccoglie tutte le informazioni dei log che in seguito vengono importate nel sistema SIEM. Questo sistema:
    * Può essere locale o ospitato in Microsoft Azure.  
    * Deve eseguire un versione x64 di Windows Server 2008 R2 SP1 o versione successiva e avere installato Microsoft .NET 4.5.1. Per determinare la versione di .NET installata, vedere [Determinare le versioni di .NET Framework installate](https://msdn.microsoft.com/library/hh925568).  
    * Deve avere connettività all'account di archiviazione di Azure usato per la registrazione di Diagnostica di Azure. Più avanti in questo articolo viene descritto come verificare la connettività.
  * Un computer da monitorare. Si tratta di una VM in esecuzione come [macchina virtuale di Azure](../virtual-machines/virtual-machines-windows-overview.md). Le informazioni di registrazione vengono inviate da questo computer al computer del servizio Integrazione log di Azure.

Per una rapida dimostrazione di come creare una macchina virtuale usando il portale di Azure, guardare il video seguente:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Durante il test, è possibile usare qualsiasi sistema che soddisfi i requisiti minimi del sistema operativo. Per un ambiente di produzione, il carico può richiedere una pianificazione per aumentare il numero di istanze o le prestazioni.

È possibile eseguire più istanze del servizio Integrazione log di Azure. È tuttavia possibile eseguire solo un'istanza del servizio per ogni macchina virtuale o computer fisico. Inoltre, è possibile bilanciare il carico degli account di archiviazione di Diagnostica di Azure per Diagnostica di Microsoft Azure. Il numero di sottoscrizioni da fornire alle istanze si basa sulla capacità.

> [!NOTE]
> Non sono attualmente disponibili indicazioni specifiche sul momento più adatto per eseguire operazioni per aumentare le istanze dei computer con Integrazione log di Azure (ovvero i computer che eseguono il servizio Integrazione log di Azure) o per le sottoscrizioni o gli account di archiviazione. Prendere decisioni relative alla scalabilità in basa alle prestazioni osservate in ognuna di queste aree.

Per migliorare le prestazioni, è anche possibile passare a un piano superiore per il servizio Integrazione log di Azure. Le metriche di prestazioni seguenti consentono di ridimensionare i computer scelti per l'esecuzione del servizio Integrazione log di Azure:

* In un computer con 8 processori (core) una singola istanza di Integrazione log di Azure può elaborare circa 24 milioni di eventi al giorno (circa 1 milione di eventi all'ora).
* In un computer con 4 processori (core) una singola istanza di Integrazione log di Azure può elaborare circa 1,5 milioni di eventi al giorno (circa 62.500 di eventi all'ora).

## <a name="install-azure-log-integration"></a>Installare Integrazione log di Azure

Eseguire la routine di installazione. Scegliere se fornire le informazioni di telemetria a Microsoft.

Il servizio Integrazione log di Azure raccoglie i dati di telemetria dal computer in cui è installato.  

I dati di telemetria raccolti includono quanto segue:

* Eccezioni che si verificano durante l'esecuzione di Integrazione log di Azure.
* Metriche relative al numero di query e di eventi elaborati.
* Statistiche sulle opzioni della riga di comando Azlog.exe usate. 

> [!NOTE]
> Si consiglia di consentire a Microsoft di raccogliere i dati di telemetria. È possibile disattivare la raccolta dei dati di telemetria deselezionando la casella di controllo **Allow Microsoft to collect telemetry data** (Consenti a Microsoft di raccogliere i dati di telemetria).
>

![Screenshot del riquadro di installazione, con la casella di controllo dei dati di telemetria selezionata](./media/security-azure-log-integration-get-started/telemetry.png)


Il processo di installazione è illustrato nel video seguente:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Passaggi successivi all'installazione e di convalida

Dopo aver completato la configurazione di base, si può procedere con i passaggi successivi all'installazione e di convalida:

1. Aprire PowerShell come amministratore. Andare quindi alla directory C:\Programmi\Integrazione log di Microsoft Azure.
2. Importare i cmdlet di Integrazione log di Azure. Per importare i cmdlet, eseguire lo script `LoadAzlogModule.ps1`. Immettere `.\LoadAzlogModule.ps1` e quindi premere INVIO. Si noti che in questo comando si usa **.\\** . Verrà visualizzata una schermata simile alla figura seguente:

   ![Screenshot dell'output del comando LoadAzlogModule.ps1](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. Configurare quindi Integrazione log di Azure per l'uso di un ambiente di Azure specifico. *Ambiente di Azure* è il tipo di data center del cloud di Azure che si vuole usare. Anche se esistono diversi ambienti di Azure, le opzioni attualmente pertinenti sono **AzureCloud** o **AzureUSGovernment**. Eseguendo PowerShell come amministratore, verificare di essere in C:\Programmi\Integrazione log di Microsoft Azure\. Eseguire quindi questo comando:

   `Set-AzlogAzureEnvironment -Name AzureCloud` (per **AzureCloud**)
  
   Per usare il cloud di Azure US Government, usare **AzureUSGovernment** per la variabile **-Name**. Non sono attualmente supportati altri cloud di Azure.  

   > [!NOTE]
   > Non si riceve feedback quando il comando ha esito positivo. 

4. Per poter monitorare un sistema, è necessario il nome dell'account di archiviazione usato per Diagnostica di Azure. Nel portale di Azure andare a **Macchine virtuali**. Cercare una macchina virtuale Windows da monitorare. Nella sezione **Proprietà** selezionare **Impostazioni di diagnostica**.  Selezionare quindi **Agente**. Prendere nota del nome dell'account di archiviazione specificato. Il nome dell'account sarà necessario per un passaggio successivo.

   ![Screenshot del riquadro delle impostazioni di Diagnostica di Azure](./media/security-azure-log-integration-get-started/storage-account-large.png) 

   ![Screenshot del pulsante Abilita monitoraggio a livello di guest](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

   > [!NOTE]
   > Se il monitoraggio non è stato abilitato quando è stata creata la macchina virtuale, è possibile abilitarlo come illustrato nell'immagine precedente.

5. Tornare ora al computer Integrazione Log di Azure. Verificare di avere la connettività all'account di archiviazione dal sistema in cui è stato installato Integrazione log di Azure. Il computer che esegue il servizio Integrazione log di Azure deve accedere all'account di archiviazione per recuperare le informazioni registrate da Diagnostica di Azure in ognuno dei sistemi monitorati. Per verificare la connettività: 
   1. [Scaricare Azure Storage Explorer](https://storageexplorer.com/).
   2. Completare la configurazione.
   3. Al termine dell'installazione, selezionare **Avanti**. Lasciare selezionata la casella di controllo **Launch Microsoft Azure Storage Explorer** (Avvia Microsoft Azure Storage Explorer).  
   4. Accedere ad Azure.
   5. Verificare che sia possibile vedere l'account di archiviazione configurato per Diagnostica di Azure: 

   ![Screenshot degli account di archiviazione in Storage Explorer](./media/security-azure-log-integration-get-started/storage-explorer.png)

   1. Alcune opzioni vengono visualizzate sotto gli account di archiviazione. Sotto **Tabelle** dovrebbe essere presente una tabella denominata **WADWindowsEventLogsTable**.

   Se il monitoraggio non è stato abilitato quando è stata creata la macchina virtuale, è possibile abilitarlo, come descritto prima.


## <a name="integrate-windows-vm-logs"></a>Integrare log di macchine virtuali Windows

In questo passaggio si configura il computer che esegue il servizio Integrazione log di Azure per la connessione all'account di archiviazione che contiene i file di log.

Per poter completare questo passaggio, sono necessari alcuni elementi:  
* **FriendlyNameForSource**: nome descrittivo che è possibile applicare all'account di archiviazione configurato per l'archiviazione delle informazioni di Diagnostica di Azure da parte della macchina virtuale.
* **StorageAccountName**: nome dell'account di archiviazione specificato durante la configurazione di Diagnostica di Azure.  
* **StorageKey**: chiave di archiviazione per l'account di archiviazione in cui le informazioni di Diagnostica di Azure vengono archiviate per questa macchina virtuale.  

Per ottenere la chiave di archiviazione, completare la procedura seguente:
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel riquadro di spostamento selezionare **Tutti i servizi**.
3. Nella casella **Filtro** immettere **Archiviazione**. Selezionare quindi **Account di archiviazione**.

   ![Screenshot che mostra l'opzione Account di archiviazione in Tutti i servizi](./media/security-azure-log-integration-get-started/filter.png)

4. Viene visualizzato un elenco degli account di archiviazione. Fare doppio clic sull'account assegnato alla risorsa di archiviazione dei log.

   ![Screenshot che mostra un elenco di account di archiviazione](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. In **Impostazioni** selezionare **Chiavi di accesso**.

   ![Screenshot che mostra l'opzione Chiavi di accesso nel menu](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. Copiare **key1** e quindi salvarla in una posizione sicura a cui sia possibile accedere per il passaggio seguente.
7. Nel server in cui è installato Integrazione log di Azure aprire una finestra del prompt dei comandi come amministratore. Assicurarsi di aprire una finestra del prompt dei comandi come amministratore e non PowerShell.
8. Andare alla directory C:\Programmi\Integrazione log di Microsoft Azure.
9. Eseguire questo comando: `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
   Esempio:
  
   `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

   Per visualizzare l'ID sottoscrizione nel codice XML dell'evento, aggiungere l'ID sottoscrizione al nome descrittivo:

   `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
   Esempio:
  
   `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Attendere 60 minuti e quindi visualizzare gli eventi che vengono estratti dall'account di archiviazione. Per visualizzare gli eventi, in Integrazione log di Azure selezionare **Visualizzatore eventi** > **Registri di Windows** > **Eventi inoltrati**.

Il video seguente illustra i passaggi precedenti:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Se i dati non vengono visualizzati nella cartella Eventi inoltrati
Se i dati non vengono visualizzati nella cartella Eventi inoltrati dopo un'ora, completare questa procedura:

1. Controllare il computer che esegue il servizio Integrazione log di Azure. Verificare che possa accedere ad Azure. Per testare la connettività, in un browser provare ad andare al [portale di Azure](https://portal.azure.com).
2. Verificare che l'account utente Azlog abbia l'autorizzazione di scrittura per la cartella users\Azlog.
   1. Aprire Esplora file.
   2. Passare a C:\users.
   3. Fare clic con il pulsante destro del mouse su C:\users\Azlog.
   4. Scegliere **Sicurezza**.
   5. Selezionare **Servizio NT\Azlog**. Controllare le autorizzazioni per l'account. Se l'account non è presente in questa scheda o se le autorizzazioni appropriate non sono visualizzate, è possibile assegnare le autorizzazioni all'account in questa scheda.
3. Quando si esegue il comando `Azlog source list`, verificare che l'account di archiviazione aggiunto nel comando `Azlog source add` sia elencato nell'output.
4. Per verificare se vengono segnalati errori dal servizio Integrazione log di Azure, passare a **Visualizzatore eventi** > **Registri di Windows** > **Applicazione**.

Se si verificano problemi durante l'installazione e la configurazione, è possibile creare una [richiesta di supporto](../azure-supportability/how-to-create-azure-support-request.md). Per il servizio, selezionare **Integrazione log**.

Un'altra possibilità per ottenere assistenza è il [forum MSDN su Integrazione log di Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Nel forum MSDN la community può fornire supporto rispondendo a domande e condividendo suggerimenti e consigli su come sfruttare al meglio Integrazione log di Azure. Anche il team di Integrazione log di Azure monitora questo forum e interverrà ogni volta che sarà possibile.

## <a name="integrate-azure-activity-logs"></a>Integrare log attività di Azure

Il log attività di Azure è un log delle sottoscrizioni che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Ciò include un intervallo di dati che vanno dai dati operativi di Azure Resource Manager agli aggiornamenti sugli eventi di integrità del servizio. In questo log sono inclusi anche gli avvisi del Centro sicurezza di Azure.
> [!NOTE]
> Prima di eseguire la procedura descritta in questo articolo, è necessario esaminare l'articolo [introduttivo](security-azure-log-integration-get-started.md) e completare la procedura in esso descritta.

### <a name="steps-to-integrate-azure-activity-logs"></a>Passaggi per l'integrazione di log attività di Azure

1. Aprire il prompt dei comandi ed eseguire questo comando: ```cd c:\Program Files\Microsoft Azure Log Integration```
2. Eseguire questo comando: ```azlog createazureid```

    Questo comando richiede le credenziali di accesso di Azure. Il comando crea quindi un'entità servizio di Azure Active Directory nei tenant di Azure AD che ospitano le sottoscrizioni di Azure in cui l'utente connesso è amministratore, coamministratore o proprietario. Se l'utente connesso è solo un utente guest nel tenant di Azure AD, il comando avrà esito negativo. L'autenticazione in Azure avviene tramite Azure AD. La creazione di un'entità servizio per l'integrazione dei log di Azure crea l'identità di Azure AD a cui verrà consentito l'accesso in lettura dalle sottoscrizioni di Azure.
3. Eseguire il comando seguente per autorizzare l'entità servizio di Integrazione log di Azure creata nel passaggio precedente ad accedere in lettura al log attività per la sottoscrizione. Per eseguire il comando è necessario essere proprietario della sottoscrizione.

   ```Azlog.exe authorize subscriptionId``` Esempio:

   ```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```

4. Verificare che siano stati creati i file JSON del log di controllo di Azure Active Directory nelle cartelle seguenti:
   - C:\Users\azlog\AzureResourceManagerJson
   - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> Per istruzioni specifiche su come includere le informazioni dei file JSON nel sistema di gestione delle informazioni e degli eventi di sicurezza (SIEM), contattare il fornitore SIEM.

L'assistenza della community è disponibile tramite il [forum MSDN di Integrazione log di Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Questo forum consente ai membri della community di aiutarsi reciprocamente con domande, risposte, suggerimenti e trucchi. Inoltre, il team di integrazione dei log di Azure monitora questo forum e offre il suo contributo quando possibile.

Si può anche aprire un [richiesta di supporto](../azure-supportability/how-to-create-azure-support-request.md). Selezionare Integrazione log come servizio per il quale si richiede il supporto.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Integrazione log di Azure, vedere gli articoli seguenti: Prima di eseguire la procedura descritta in questo articolo, è necessario esaminare l'articolo introduttivo e completare la procedura in esso descritta.

* [Introduzione a Integrazione log di Azure](security-azure-log-integration-overview.md). Questo articolo presenta Integrazione log di Azure, le funzionalità chiave e il funzionamento.
* [Partner configuration steps (Procedura per la configurazione partner)](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Questo post di blog illustra come configurare Integrazione log di Azure per usare le soluzioni partner Splunk, HP ArcSight e IBM QRadar. Descrive le linea guida correnti sulla configurazione dei componenti SIEM. Per altri dettagli, contattare il fornitore SIEM.
* [Domande frequenti su Integrazione log di Azure](security-azure-log-integration-faq.md). Queste domande frequenti riguardano Integrazione log di Azure.
* [Integrazione degli avvisi del Centro sicurezza di Azure con Integrazione log di Azure](../security-center/security-center-integrating-alerts-with-log-integration.md). Questo articolo illustra come sincronizzare gli avvisi del Centro sicurezza e gli eventi di sicurezza delle macchine virtuali raccolti da Diagnostica di Azure e i log attività di Azure. Sincronizzare i log usando la propria soluzione SIEM o log di monitoraggio di Azure.
* [New features for Azure Diagnostics and Azure audit logs (Nuove funzionalità per Diagnostica di Azure e i log di controllo di Azure)](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). Questo post di blog presenta i log di controllo di Azure e altre funzionalità che consentono di ottenere informazioni dettagliate sulle operazioni delle risorse di Azure.
