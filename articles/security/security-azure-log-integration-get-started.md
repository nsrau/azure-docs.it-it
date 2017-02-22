---
title: Introduzione all&quot;integrazione dei log di Azure | Documentazione Microsoft
description: Informazioni su come installare il servizio di integrazione dei log di Azure e integrare i log dall&quot;archiviazione di Azure, i log di controllo di Azure e gli avvisi del Centro sicurezza di Azure.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 01/07/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: aaa69e2e4fed314e8bc363f60e7538b12bb3a56d
ms.openlocfilehash: ca7f05534113752f3607268c15a9fe3e0e2982e0


---
# <a name="get-started-with-azure-log-integration-preview"></a>Introduzione all'integrazione dei log di Azure (Anteprima)
L'integrazione dei log di Azure consente di integrare log non elaborati delle risorse di Azure nei sistemi di gestione di informazioni ed eventi di sicurezza locali. Questa integrazione fornisce un dashboard unificato per tutti gli asset, locali o su cloud, consentendo di aggregare, correlare, analizzare e inviare avvisi per gli eventi di sicurezza associati alle applicazioni.

Questa esercitazione illustra come installare l'integrazione dei log di Azure e integrare i log dall'archiviazione di Azure, i log di controllo di Azure e gli avvisi del Centro sicurezza di Azure. Il tempo previsto per il completamento dell'esercitazione è di un'ora.

## <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un computer (locale o nel cloud) per installare il servizio di integrazione dei log di Azure. Questo computer deve eseguire un sistema operativo Windows a 64 bit con installato .Net 4.5.1. Il computer viene chiamato **integratore Azlog**.
* Sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/).
* La diagnostica di Azure abilitata per le macchine virtuali (VM) di Azure. Per abilitare la diagnostica per i servizi cloud, vedere [Abilitazione di Diagnostica di Azure in servizi cloud di Azure](../cloud-services/cloud-services-dotnet-diagnostics.md). Per abilitare la diagnostica per una VM di Azure che esegue Windows, vedere [Usare PowerShell per abilitare Diagnostica di Azure in una macchina virtuale che esegue Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Connettività dall'integratore azlog all'archiviazione di Azure e autenticazione e autorizzazione alla sottoscrizione di Azure.
* Per i log VM di Azure, l'agente SIEM (ad esempio Splunk Universal Forwarder, l'agente HP ArcSight Windows Event Collector o IBM QRadar WinCollect) deve essere installato sull'integratore Azlog.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione
È possibile eseguire più istanze dell'integratore Azlog se il volume di eventi è elevato. Il bilanciamento del carico degli account di archiviazione di Diagnostica di Azure per Windows *(WAD)* e il numero di sottoscrizioni da fornire alle istanze devono essere basati sulla capacità.

Su un computer con 8 processori (memoria centrale), una singola istanza dell'integratore Azlog può elaborare circa 24 milioni di eventi al giorno (all'incirca 1 milione all'ora).

Su un computer con 4 processori (memoria centrale), una singola istanza dell'integratore Azlog può elaborare circa 1,5 milioni di eventi al giorno (all'incirca 62,5 K all'ora).

## <a name="install-azure-log-integration"></a>Installare l'integrazione dei log di Azure
Scaricare l' [integrazione dei log di Azure](https://www.microsoft.com/download/details.aspx?id=53324).

Il servizio di integrazione dei log di Azure raccoglie i dati di telemetria dal computer in cui è installato.  I dati di telemetria raccolti sono:

* Eccezioni che si verificano durante l'esecuzione dell'integrazione dei log di Azure
* Metriche relative al numero di query e di eventi elaborati
* Statistiche sulle opzioni della riga di comando Azlog.exe che vengono usate

> [!NOTE]
> Per disabilitare la raccolta dei dati di telemetria è possibile deselezionare questa opzione.
>
>


## <a name="set-your-azure-environment"></a>Configurare l'ambiente Azure
1. Aprire la console di PowerShell come amministratore ed eseguire il comando **cd** per passare alla directory **c:\Programmi\Microsoft Azure Log Integration**.
2. Eseguire il comando Set-AzLogAzureEnvironment -Name <Cloud>

       Replace the Cloud with any of the following
       AzureCloud
       AzureChinaCloud
       AzureUSGovernment
       AzureGermanCloud

       Note that at this time, an Azlog integrator only supports integrating logs from one cloud that you choose to integrate.

## <a name="integrate-azure-vm-logs-from-your-azure-diagnostics-storage-accounts"></a>Integrare i registri VM di Azure dagli account di archiviazione di Diagnostica di Azure
1. Prima di continuare con l'integrazione dei log di Azure, verificare i prerequisiti elencati sopra per assicurarsi che l'account di archiviazione WAD stia raccogliendo i log. Se l'account di archiviazione WAD non sta raccogliendo i log, non eseguire i passaggi seguenti.
2. Aprire il prompt dei comandi e digitare **cd** per passare alla directory **c:\Program Files\Microsoft Azure Log Integration**.
3. Eseguire il comando

        azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>

      Sostituire StorageAccountName con il nome dell'account di archiviazione di Azure configurato per ricevere eventi di diagnostica da una VM.

        azlog source add azlogtest WAD azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==

      Se si vuole che l'ID sottoscrizione venga visualizzato nell'XML dell'evento, aggiungere l'ID sottoscrizione al nome descrittivo:

        azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>
4. Attendere 30-60 minuti (l'operazione potrebbe richiedere fino a un'ora), quindi visualizzare gli eventi che vengono estratti dall'account di archiviazione. Per visualizzarli, aprire **Visualizzatore eventi > Registri di Windows > Eventi inoltrati** nell'integratore Azlog.
5. Assicurarsi che il connettore SIEM standard installato nel computer sia configurato per prelevare gli eventi dalla cartella **Eventi inoltrati** e inviarli tramite pipe all'istanza di SIEM. Esaminare la configurazione specifica SIEM per configurare e visualizzare l'integrazione dei log.

## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>Se i dati non vengono visualizzati nella cartella Eventi inoltrati?
Se, dopo un'ora, i dati non vengono visualizzati nella cartella **Eventi inoltrati** , procedere come segue:

1. Controllare il computer e verificare che possa accedere ad Azure. Per verificare la connettività, provare ad aprire il [portale di Azure](http://portal.azure.com) dal browser.
2. Verificare che l'account utente **azlog** abbia l'autorizzazione di scrittura per la cartella **users\azlog**.
3. Verificare che l'account di archiviazione aggiunto nel comando **azlog source add** sia elencato quando si esegue il comando **azlog source list**.
4. Passare a **Visualizzatore eventi > Registri di Windows > Applicazione** per vedere se sono presenti errori segnalati dall'integrazione del log di Azure.

Se gli eventi non compaiono ancora, procedere come segue:

1. Scaricare [Microsoft Azure Storage Explorer](http://storageexplorer.com/).
2. Connettersi all'account di archiviazione aggiunto nel comando **azlog source add**.
3. In Microsoft Azure Storage Explorer passare alla tabella **WADWindowsEventLogsTable** per vedere se contiene dati. Se non è così, significa che la diagnostica nella VM non è configurata correttamente.

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrare i log di controllo di Azure e gli avvisi del Centro sicurezza di Azure
1. Aprire il prompt dei comandi e digitare **cd** per passare alla directory **c:\Program Files\Microsoft Azure Log Integration**.
2. Eseguire il comando

        azlog createazureid

      Questo comando richiede le credenziali di accesso di Azure. Il comando crea quindi un' [entità servizio di Azure Active Directory](../active-directory/active-directory-application-objects.md) nei tenant di Azure AD che ospitano le sottoscrizioni di Azure in cui l'utente connesso è amministratore, coamministratore o proprietario. Se l'utente connesso è solo un utente Guest nel tenant di Azure AD, il comando avrà esito negativo. L'autenticazione in Azure avviene tramite Azure Active Directory (AD).  La creazione di un'entità servizio per l'integrazione dei log di Azure crea l'identità di Azure AD a cui verrà consentito l'accesso in lettura dalle sottoscrizioni di Azure.
3. Eseguire il comando

        azlog authorize <SubscriptionID>

      In questo modo si assegna l'accesso in lettura per la sottoscrizione all'entità servizio creata nel passaggio 2. Se non si specifica un ID sottoscrizione, viene tentata l'assegnazione del ruolo Lettore all'entità servizio per tutte le sottoscrizioni verso cui si dispone di qualsiasi tipo di accesso.

        azlog authorize 0ee9d577-9bc4-4a32-a4e8-c29981025328

   > [!NOTE]
   > Se si esegue il comando **authorize** subito dopo il comando c**createazureid**, potrebbero essere visualizzati avvisi. Tra il momento in cui viene creato l'account Azure AD e quello in cui l'account è disponibile per l'uso c'è una certa latenza. Per non visualizzare tali avvisi, attendere circa 10 secondi tra l'esecuzione del comando **createazureid** e l'esecuzione del comando **authorize**.
   >
   >
4. Verificare che nelle cartelle seguenti siano presenti i file JSON del log di controllo:

   * **c:\Users\azlog\AzureResourceManagerJson**
   * **c:\Users\azlog\AzureResourceManagerJsonLD**
5. Verificare che nelle cartelle seguenti siano presenti avvisi del Centro sicurezza di Azure:

   * **c:\Users\azlog\ AzureSecurityCenterJson**
   * **c:\Users\azlog\AzureSecurityCenterJsonLD**
6. Puntare il connettore del server di inoltro del file SIEM standard alla cartella appropriata per reindirizzare i dati all'istanza di SIEM. A seconda del prodotto SIEM in uso, potrebbe essere necessario il mapping dei campi.

In caso di domande sull'integrazione dei log di Azure, inviare un messaggio di posta elettronica all'indirizzo [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato descritto come installare l'integrazione dei log di Azure e integrare i log dall'archiviazione di Azure. Per altre informazioni, vedere gli argomenti seguenti:

* [Integrazione dei log di Microsoft Azure per i log di Azure (Anteprima)](https://www.microsoft.com/download/details.aspx?id=53324) – Area download per informazioni dettagliate, requisiti di sistema e istruzioni di installazione sull'integrazione dei log di Azure.
* [Introduzione all'integrazione dei log di Azure](security-azure-log-integration-overview.md) – Questo documento presenta l'integrazione dei log di Azure, le funzionalità chiave e il funzionamento.
* [Passaggi per la configurazione dei partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – Questo post di blog mostra come configurare l'integrazione dei log di Azure per lavorare con le soluzioni partner Splunk, HP ArcSight e IBM QRadar.
* [Domande frequenti sull'integrazione dei log di Azure](security-azure-log-integration-faq.md) - Queste domande frequenti riguardano l'integrazione dei log di Azure.
* [Integrazione degli avvisi del Centro sicurezza con l'integrazione dei log di Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) - Questo documento mostra come sincronizzare gli avvisi del Centro sicurezza di Azure, insieme agli eventi di sicurezza delle macchine virtuali raccolti da Diagnostica di Azure e dai log di controllo di Azure, con la propria soluzione SIEM o di analisi dei log.
* [Nuove funzionalità per Diagnostica di Azure e i log di controllo di Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) - Questo post di blog presenta i log di controllo di Azure e altre funzionalità che consentono di ottenere informazioni dettagliate sulle operazioni delle risorse di Azure.



<!--HONumber=Jan17_HO4-->


