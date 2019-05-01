---
title: Advanced Threat Protection per Archiviazione di Azure
description: Configurare Advanced Threat Protection di Archiviazione di Microsoft Azure per rilevare le anomalie nelle attività dell'account e ricevere una notifica in caso di tentativi di accesso all'account potenzialmente dannosi.
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: monhaber
ms.manager: shaik
ms.openlocfilehash: c42867ff7aea2210f20a2cd2adb5c067b8f36c80
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926512"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Advanced Threat Protection per Archiviazione di Azure

Advanced Threat Protection per Archiviazione di Azure offre un livello aggiuntivo di intelligence di sicurezza in grado di rilevare tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento degli account di archiviazione. Questo livello di protezione consente di affrontare le minacce senza dover essere esperti di sicurezza o dover gestire sistemi di monitoraggio della sicurezza. 

Quando si verificano le anomalie nelle attività, vengono generati avvisi di sicurezza.  Questi avvisi di sicurezza sono integrati con [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/)e vengono anche inviati tramite posta elettronica per gli amministratori delle sottoscrizioni, con i dettagli dell'attività sospetta e consigli su come analizzare e correggere le minacce.

> [!NOTE]
> * Advanced Threat Protection per l'archiviazione di Azure è attualmente disponibile solo per l'archiviazione Blob.
> * Per dettagli sui prezzi, incluse una versione di valutazione gratuita di 30 giorni, vedere la [pagina dei prezzi Azure Security Center]( https://azure.microsoft.com/pricing/details/security-center/).
> * Per la funzionalità di archiviazione di Azure ATP non è attualmente disponibile in Azure per enti pubblici e aree del cloud con sovranità.

Advanced Threat Protection per l'archiviazione di Azure inserisce i log di diagnostica di lettura, scrittura ed eliminazione delle richieste nell'archivio Blob per il rilevamento delle minacce. Per analizzare gli avvisi da Advanced Threat Protection, è possibile visualizzare le attività di archiviazione correlato usando registrazione Analitica dell'archiviazione. Per altre informazioni, vedere come [configurare la registrazione di archiviazione Analitica](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Configurazione di Advanced Threat Protection 

### <a name="using-the-portal"></a>Tramite il portale

1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com/).

2. Passare alla pagina di configurazione dell'account di Archiviazione di Microsoft Azure che si vuole proteggere. Nella pagina **Impostazioni**, selezionare **Advanced Threat Protection**.

3. Nel pannello di configurazione **Advanced Threat Protection**
    * **Attivare** Advanced *Threat Protection*
    * Fare clic su **Salva** per salvare i criteri di Advanced Threat Protection nuovi o aggiornati. (I prezzi indicati nell'immagine sono ad esempio è solo a scopo).

![Attivare la protezione avanzata dalle minacce di Archiviazione di Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Utilizzo del Centro sicurezza di Azure
Quando effettua la sottoscrizione al livello Standard in Centro sicurezza di Azure Advanced Threat Protection viene impostato su account di archiviazione. Per altre informazioni, vedere [esegue l'aggiornamento al livello Standard del Centro sicurezza per la sicurezza avanzata](https://docs.microsoft.com/azure/security-center/security-center-pricing). (I prezzi indicati nell'immagine sono ad esempio è solo a scopo).

![Livello standard in Centro sicurezza di AZURE](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing.png)

### <a name="using-azure-resource-manager-templates"></a>Uso dei modelli di Gestione risorse di Azure

Usare un modello di Azure Resource Manager per distribuire un account di archiviazione di Azure con Advanced Threat Protection è abilitato.
Per altre informazioni, vedere [account di archiviazione con Advanced Threat Protection](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>Utilizzo di criteri di Azure

Usare criteri di Azure per abilitare Advanced Threat Protection tra account di archiviazione in un specificato sottoscrizione o gruppo di risorse.

1. Avvio di Azure **criteri - definizioni** pagina.

1. Cercare il **distribuire Advanced Threat Protection per gli account di archiviazione** criteri.

     ![Criteri di ricerca](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Selezionare un gruppo di risorse o sottoscrizione di Azure.

    ![Selezionare la sottoscrizione o gruppo](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Assegnare i criteri.

    ![Pagina delle definizioni dei criteri](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>Uso dell'API REST
Usare i comandi API Rest per creare, aggiornare o ottenere l'impostazione di Advanced Threat Protection per un account di archiviazione specifico.

* [Creazione di Advanced Threat Protection:](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Advanced Threat Protection: introduzione](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Uso di Azure PowerShell

Usare i cmdlet di PowerShell seguenti:

  * [Abilita Advanced Threat Protection](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Ottenere Advanced Threat Protection](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Disabilitare la protezione avanzata dalle minacce](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Esplora le anomalie di sicurezza

Quando si verificano anomalie nelle attività di archiviazione, si riceve una e-mail di notifica con le informazioni sull'evento sospetto di sicurezza. I dettagli sull'evento comprendono:

* La natura dell'anomalia
* nome dell'account di archiviazione
* L'ora dell'evento
* Il tipo di archiviazione
* Le cause potenziali 
* I passaggi di analisi
* La procedura di correzione


L'e-mail fornisce inoltre informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia.

![E-mail di avviso di protezione avanzata dalle minacce di Archiviazione di Microsoft Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

È possibile rivedere e gestire gli avvisi di sicurezza correnti dal [riquadro degli avvisi di sicurezza](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts) del Centro sicurezza di Azure. Facendo clic su un avviso specifico vengono visualizzati altri dettagli e azioni per analizzare la minaccia e risolvere eventuali minacce future.

![E-mail di avviso di protezione avanzata dalle minacce di Archiviazione di Microsoft Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Avvisi di protezione

Gli avvisi sono generati dai tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento degli account di archiviazione. Questi eventi possono attivare gli avvisi seguenti:

### <a name="anomalous-access-pattern-alerts"></a>Avvisi di criterio di accesso anomalo

* **Accesso da posizione insolita**: Questo avviso viene generato quando viene apportata una modifica nel criterio di accesso a un account di archiviazione. Ad esempio, quando un utente effettua l'accesso ad un account di archiviazione da una posizione geografica insolita.
Possibili cause:
   * Un utente malintenzionato ha accesso l'account di archiviazione
   * Un utente autorizzato ha accesso l'account di archiviazione da una nuova posizione
 
* **Applicazione anomalie**: Questo avviso indica che un'applicazione insolita è accedere a questo account di archiviazione. Possibili cause:
   * Un utente malintenzionato ha accesso l'account di archiviazione tramite una nuova applicazione.
   * Un utente autorizzato ha utilizzato un nuova applicazione/browser per accedere all'account di archiviazione.

* **L'accesso anonimo**: Questo avviso indica che si verifica un cambiamento nel criterio di accesso a un account di archiviazione. Ad esempio, questo account è stato consentito l'accesso anonimo (vale a dire senza alcuna autenticazione), che non è previsto confrontato con il modello di accesso recente su questo account.
Possibili cause:
   * Un utente malintenzionato ha sfruttato l'accesso in lettura pubblico per un contenitore.
   * Un utente legittimo o un'applicazione ha usato accesso in lettura pubblico per un contenitore.

### <a name="anomalous-extractupload-alerts"></a>Avvisi di estrarre o caricare anomale

* **Esfiltrazione dei dati**: Questo avviso indica che una quantità insolitamente elevata di dati è stato estratto rispetto all'attività recente su questo contenitore di archiviazione. Possibili cause:
   * Un utente malintenzionato ha estratto una grande quantità di dati da un contenitore. (Ad esempio: l'esfiltrazione/violazione dei dati, non autorizzati di trasferimento dei dati)
   * Un utente legittimo o un'applicazione ha estratto una quantità insolita di dati da un contenitore. (Ad esempio: attività di manutenzione)

* **Eliminazione imprevista**: Questo avviso indica che si è verificato uno o più operazioni di eliminazione imprevista in un account di archiviazione, rispetto all'attività recente su questo account. Possibili cause:
   * Un utente malintenzionato ha eliminato i dati dall'account di archiviazione.
   * Un utente autorizzato ha eseguito un'eliminazione insolita.

* **Carica pacchetto del servizio Cloud Azure**: Questo avviso indica che un pacchetto di servizio Cloud di Azure (file con estensione cspkg) sia stato caricato in un account di archiviazione in modo anomalo, rispetto all'attività recente su questo account. Possibili cause: 
   * Un utente malintenzionato ha stato preparazione alla distribuzione di codice dannoso dall'account di archiviazione a un servizio cloud di Azure.
   * Un utente autorizzato ha stato preparazione per la distribuzione di un servizio legittimo.

### <a name="suspicious-storage-activities-alerts"></a>Avvisi di attività sospette di archiviazione

* **Modifica alle autorizzazioni di accesso**: Questo avviso indica che le autorizzazioni di accesso di questo contenitore di archiviazione sono state modificate in modo anomalo. Possibili cause: 
   * Un utente malintenzionato ha modificato le autorizzazioni del contenitore a indebolire la sicurezza.
   * Un utente legittimo ha modificato le autorizzazioni del contenitore.

* **Accedere a ispezione**: Questo avviso indica che sono state controllate le autorizzazioni di accesso di un account di archiviazione in modo anomalo, rispetto all'attività recente su questo account. Possibili cause: 
   * Un utente malintenzionato ha eseguito l'esplorazione di eventuali attacchi futuri.
   * Un utente autorizzato ha eseguito la manutenzione dell'account di archiviazione.

* **Esplorazione dei dati**: Questo avviso indica che i BLOB o contenitori in un account di archiviazione sono stati enumerati in modo anomalo, rispetto all'attività recente su questo account. Possibili cause: 
   * Un utente malintenzionato ha eseguito l'esplorazione di eventuali attacchi futuri.
   * Un utente legittimo o per la logica dell'applicazione è stati illustrati i dati all'interno dell'account di archiviazione.






## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [log negli account di archiviazione di Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Altre informazioni sul [Centro sicurezza di Azure](../../security-center/security-center-intro.md)
