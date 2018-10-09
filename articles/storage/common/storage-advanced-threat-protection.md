---
title: Monitoraggio minacce di Archiviazione di Microsoft Azure
description: Configurare Advanced Threat Protection di Archiviazione di Microsoft Azure per rilevare le anomalie nelle attività dell'account e ricevere una notifica in caso di tentativi di accesso all'account potenzialmente dannosi.
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 09/24/2018
ms.author: ronmat
ms.manager: shaik
ms.openlocfilehash: 8b2ca2d5d6418d68cab847df80fc437e468249ed
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995645"
---
# <a name="azure-storage-advanced-threat-protection"></a>Advanced Threat Protection di Archiviazione di Microsoft Azure

Advanced Threat Protection di Archiviazione di Microsoft Azure rileva le anomalie nelle attività dell'account e riceve una notifica in caso di tentativi di accesso all'account potenzialmente dannosi. Questo livello di protezione consente di affrontare le minacce senza dover essere esperti di sicurezza o dover gestire sistemi di monitoraggio della sicurezza.

Le minacce vengono rilevate definendo avvisi di sicurezza che vengono attivati quando si verificano anomalie nelle attività. Questi avvisi sono integrati al [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), che comprende i dettagli delle attività sospette ed elementi consigliati su come individuare e risolvere le minacce. 

> [!NOTE]
> Advanced Threat Protection di Archiviazione di Microsoft Azure è attualmente disponibile solo per il servizio BLOB. Gli avvisi di sicurezza sono integrati al Centro sicurezza di Azure e vengono inviati tramite e-mail agli amministratori delle sottoscrizioni.

Advanced Threat Protection di Archiviazione di Microsoft Azure inserisce i log di diagnostica di lettura, scrittura ed eliminazione delle richieste nel servizio BLOB per il rilevamento delle minacce. Per esaminare gli avvisi da Advanced Threat Protection, è necessario [configurare i log di diagnostica](storage-monitor-storage-account.md#configure-logging) per abilitare tutti i livelli di log per il servizio BLOB.

## <a name="set-up-advanced-threat-protection-in-the-portal"></a>Configurare Advanced Threat Protection nel portale

1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com/).

2. Passare alla pagina di configurazione dell'account di Archiviazione di Microsoft Azure che si vuole proteggere. Nella pagina **Impostazioni**, selezionare **Advanced Threat Protection**.

3. Nel pannello di configurazione **Advanced Threat Protection**
    * **Attivare** Advanced *Threat Protection*
    * Fare clic su **Salva** per salvare i criteri di Advanced Threat Protection nuovi o aggiornati.

![Attivare la protezione avanzata dalle minacce di Archiviazione di Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

## <a name="explore-anomalies"></a>Esplorare anomalie

Quando si verificano anomalie nelle attività di archiviazione, si riceve una e-mail di notifica con le informazioni sull'evento sospetto di sicurezza. I dettagli sull'evento comprendono:

* natura dell'anomalia
* nome dell'account di archiviazione
* tipo di archiviazione
* ora dell'evento

L'e-mail fornisce inoltre informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia.

![E-mail di avviso di protezione avanzata dalle minacce di Archiviazione di Microsoft Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

È possibile rivedere e gestire gli avvisi di sicurezza correnti dal [riquadro degli avvisi di sicurezza](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts) del Centro sicurezza di Azure. Facendo clic su un avviso specifico vengono visualizzati altri dettagli e azioni per analizzare la minaccia e risolvere eventuali minacce future.

![E-mail di avviso di protezione avanzata dalle minacce di Archiviazione di Microsoft Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Avvisi di protezione

Gli avvisi sono generati dai tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento degli account di archiviazione. Questi eventi possono attivare gli avvisi seguenti:

* **Accesso da posizione insolita**: questo avviso viene attivato quando è apportata una modifica nel criterio di accesso a un account di archiviazione. Ad esempio, quando un utente effettua l'accesso ad un account di archiviazione da una posizione geografica insolita. In alcuni casi, l'avviso rileva un'azione legittima (una nuova applicazione o un'operazione di manutenzione da parte dello sviluppatore). In altri casi, l'avviso rileva un'azione dannosa (da parte di un ex dipendente, un utente malintenzionato esterno, ecc.).

* **Estrazione dati insolita**: questo avviso viene attivato quando viene apportata una modifica nel criterio di estrazione dei dati da un account di archiviazione. Ad esempio, se un utente effettua l'accesso ad una quantità di dati insolita in un account di archiviazione. In alcuni casi, l'avviso rileva un'azione legittima (attività di manutenzione). In altri casi, l'avviso rileva un'azione dannosa (esfiltrazione/violazione dei dati, trasferimento dei dati non autorizzato).

* **Accesso da posizione insolita**: questo avviso viene attivato quando viene apportata una modifica nel criterio di accesso a un account di archiviazione. Ad esempio, si supponga che un utente effettua un accesso in modo anonimo ad un account di archiviazione. In alcuni casi, l'avviso rileva un accesso legittimo usando accesso pubblico in lettura. In altri casi, l'avviso rileva accessi non autorizzati che sfruttano l'accesso pubblico in lettura per un contenitore e i relativi BLOB.

* **Eliminazione imprevista:** questo avviso viene attivato quando si verificano uno o più operazioni impreviste di eliminazione di un account di archiviazione, basate sull'analisi cronologica dell'account di archiviazione. Ad esempio, si supponga che un utente esegua l'operazione *DeleteBlob* usando una nuova applicazione e un nuovo indirizzo IP. In alcuni casi, l'avviso rileva un'azione legittima (l'amministratore ha usato un browser diverso durante il viaggio in business). In altri casi, l'avviso rileva un'azione dannosa (un utente malintenzionato che elimina i dati). 
 
* **Modifica delle autorizzazioni di accesso:** questo avviso viene attivato quando è apportata una modifica imprevista dell'autorizzazione di accesso a un account di archiviazione. Ad esempio, supponiamo che un membro abbia modificato l'autorizzazione di accesso a un account di archiviazione usando una nuova applicazione e da un nuovo indirizzo IP. In alcuni casi, l'avviso rileva un'azione legittima (l'amministratore ha usato un browser diverso durante il viaggio in business). In altri casi, l'avviso rileva un'azione dannosa (ad esempio, un utente malintenzionato che aumenta i privilegi di un account per il quale ha ottenuto l'accesso). 

* **Carica pacchetto del servizio cloud di Azure:** questo avviso viene attivato quando è presente un'operazione di caricamento imprevisto di un pacchetto di servizio cloud di Azure (file con estensione *.cspkg*) su un account di archiviazione. Si supponga, ad esempio, che un file con estensione *.cspkg* sia stato caricato da un nuovo indirizzo IP. In alcuni casi, l'avviso rileva un'azione legittima. In altri casi, l'avviso rileva un'azione dannosa (ad esempio, un pacchetto del servizio cloud è caricato per la preparazione di una distribuzione di un servizio dannoso).    
   

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Log negli account di Archiviazione di Microsoft Azure ](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Altre informazioni sul [Centro sicurezza di Azure](../../security-center/security-center-intro.md)