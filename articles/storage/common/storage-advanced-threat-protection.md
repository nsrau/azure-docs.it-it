---
title: Advanced Threat Protection per Archiviazione di Azure
description: Configurare Advanced Threat Protection di Archiviazione di Microsoft Azure per rilevare le anomalie nelle attività dell'account e ricevere una notifica in caso di tentativi di accesso all'account potenzialmente dannosi.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 7d4f36be51591d6be2b4c42eb8a8950ab52a0258
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782572"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Advanced Threat Protection per Archiviazione di Azure

Advanced Threat Protection per Archiviazione di Azure offre un livello aggiuntivo di intelligence di sicurezza in grado di rilevare tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento degli account di archiviazione. Questo livello di protezione consente di affrontare le minacce senza dover essere esperti di sicurezza o dover gestire sistemi di monitoraggio della sicurezza. 

Gli avvisi di sicurezza vengono attivati quando si verificano anomalie nelle attività.  Questi avvisi di sicurezza sono integrati con il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/)e vengono inviati anche tramite posta elettronica agli amministratori della sottoscrizione, con informazioni dettagliate sulle attività sospette e consigli su come analizzare e correggere le minacce.

> [!NOTE]
> * Advanced Threat Protection per archiviazione di Azure è attualmente disponibile solo per l'archiviazione BLOB.
> * Per informazioni dettagliate sui prezzi, inclusa una versione di valutazione gratuita di 30 giorni, vedere la [pagina dei prezzi del Centro sicurezza di Azure]( https://azure.microsoft.com/pricing/details/security-center/).
> * La funzionalità ATP per archiviazione di Azure non è attualmente disponibile in Azure per enti pubblici e aree cloud sovrane.

Advanced Threat Protection per archiviazione di Azure inserisce i log di diagnostica delle richieste di lettura, scrittura ed eliminazione nell'archivio BLOB per il rilevamento delle minacce. Per esaminare gli avvisi da Advanced Threat Protection, è possibile visualizzare le attività di archiviazione correlate usando Analisi archiviazione registrazione. Per ulteriori informazioni, vedere come [configurare la registrazione analisi archiviazione](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Configurare Advanced Threat Protection 

### <a name="using-the-portal"></a>Tramite il portale

1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com/).

2. Passare alla pagina di configurazione dell'account di Archiviazione di Microsoft Azure che si vuole proteggere. Nella pagina **Impostazioni**, selezionare **Advanced Threat Protection**.

3. Nel pannello di configurazione **Advanced Threat Protection**
    * **Attivare** Advanced *Threat Protection*
    * Fare clic su **Salva** per salvare i criteri di Advanced Threat Protection nuovi o aggiornati. I prezzi nell'immagine sono solo a scopo esemplificativo.

![Attivare la protezione avanzata dalle minacce di Archiviazione di Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Utilizzo del Centro sicurezza di Azure

Quando si sottoscrive il livello standard nel centro sicurezza di Azure, Advanced Threat Protection viene configurato automaticamente in tutti gli account di archiviazione. È possibile abilitare o disabilitare Advanced Threat Protection per gli account di archiviazione in una sottoscrizione specifica, come indicato di seguito:

1. Avviare il **Centro sicurezza di Azure** nella [portale di Azure](https://portal.azure.com).
1. Nel menu principale fare clic su **prezzi & impostazioni**.
1. Fare clic sulla sottoscrizione che si vuole abilitare o disabilitare la protezione dalle minacce per gli account di archiviazione.

    ![Selezionare la sottoscrizione](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Fare clic su **Piano tariffario**.
1. Nella sezione **selezionare il piano tariffario per tipo di risorsa** , nella riga **account di archiviazione** , fare clic su **abilitato** o **disabilitato**.

    ![Abilita ATP nel centro sicurezza](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Fare clic su **Save**.

### <a name="using-azure-resource-manager-templates"></a>Uso dei modelli di Gestione risorse di Azure

Usare un modello di Azure Resource Manager per distribuire un account di archiviazione di Azure con Advanced Threat Protection abilitata. Per altre informazioni, vedere [account di archiviazione con Advanced Threat Protection](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>Uso di criteri di Azure

Usare un criterio di Azure per abilitare la protezione avanzata dalle minacce tra gli account di archiviazione in una sottoscrizione o un gruppo di risorse specifico.

1. Avviare la pagina **criteri di Azure-definizioni** .

1. Cercare il criterio **Distribuisci Advanced Threat Protection in account di archiviazione** .

     ![Criteri di ricerca](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Selezionare una sottoscrizione o un gruppo di risorse di Azure.

    ![Selezionare una sottoscrizione o un gruppo](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Assegnare i criteri.

    ![Pagina definizioni criteri](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>Uso dell'API REST
Usare i comandi dell'API REST per creare, aggiornare o ottenere l'impostazione Advanced Threat Protection per un account di archiviazione specifico.

* [Advanced Threat Protection-crea](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Advanced Threat Protection-Ottieni](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Uso di Azure PowerShell

Usare i cmdlet di PowerShell seguenti:

  * [Abilita Advanced Threat Protection](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Ottenere Advanced Threat Protection](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Disabilitare Advanced Threat Protection](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Esplorare le anomalie di sicurezza

Quando si verificano anomalie nelle attività di archiviazione, si riceve una e-mail di notifica con le informazioni sull'evento sospetto di sicurezza. I dettagli sull'evento comprendono:

* Natura dell'anomalia
* nome dell'account di archiviazione
* Ora dell'evento
* Tipo di archiviazione
* Possibili cause 
* Passaggi dell'indagine
* Procedura di correzione


L'e-mail fornisce inoltre informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia.

![E-mail di avviso di protezione avanzata dalle minacce di Archiviazione di Microsoft Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

È possibile rivedere e gestire gli avvisi di sicurezza correnti dal [riquadro degli avvisi di sicurezza](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts) del Centro sicurezza di Azure. Facendo clic su un avviso specifico vengono visualizzati altri dettagli e azioni per analizzare la minaccia e risolvere eventuali minacce future.

![E-mail di avviso di protezione avanzata dalle minacce di Archiviazione di Microsoft Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Avvisi di protezione

Gli avvisi sono generati dai tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento degli account di archiviazione. Per un elenco di questi avvisi, vedere avvisi di [archiviazione di Azure](../../security-center/security-center-alerts-data-services.md#azure-storage)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui [log negli account di archiviazione di Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Altre informazioni sul [Centro sicurezza di Azure](../../security-center/security-center-intro.md)
