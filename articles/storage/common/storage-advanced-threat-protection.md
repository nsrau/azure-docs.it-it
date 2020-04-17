---
title: Configurare la protezione avanzata dalle minacce
titleSuffix: Azure Storage
description: Configurare la protezione avanzata dalle minacce per Archiviazione di Azure per rilevare le anomalie nell'attività dell'account e ricevere una notifica dei tentativi potenzialmente dannosi di accedere all'account.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: f390aececdbf9a20f191279892c1856332f2aa8e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482088"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Configurare la protezione avanzata dalle minacce per Archiviazione di AzureConfigure advanced threat protection for Azure Storage

La protezione avanzata dalle minacce per Archiviazione di Azure offre un ulteriore livello di intelligenza di sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account di archiviazione. Questo livello di protezione consente di affrontare le minacce senza essere esperti di sicurezza o gestire sistemi di monitoraggio della sicurezza.

Gli avvisi di sicurezza vengono attivati quando si verificano anomalie nell'attività. Questi avvisi di sicurezza sono integrati con il [Centro sicurezza](https://azure.microsoft.com/services/security-center/)di Azure e vengono inoltre inviati tramite posta elettronica agli amministratori della sottoscrizione, con dettagli sulle attività sospette e consigli su come analizzare e correggere le minacce.

Il servizio inviera i log di diagnostica delle richieste di lettura, scrittura ed eliminazione nell'archivio BLOB per il rilevamento delle minacce. Per analizzare gli avvisi relativi alla protezione avanzata dalle minacce, è possibile visualizzare l'attività di archiviazione correlata usando la registrazione di Analisi archiviazione. Per altre informazioni, vedere **Configurare la registrazione** in Monitorare un account di archiviazione nel portale di Azure.For more information, see Configure logging in [Monitor a storage account in the Azure portal.](storage-monitor-storage-account.md#configure-logging)

## <a name="availability"></a>Disponibilità

La protezione avanzata dalle minacce per Archiviazione di Azure è attualmente disponibile solo per [L'archiviazione BLOB.](https://azure.microsoft.com/services/storage/blobs/) I tipi di account che supportano la protezione avanzata dalle minacce includono la versione 2 generica, il BLOB a blocchi e gli account di archiviazione BLOB. La protezione avanzata dalle minacce è disponibile in tutti i cloud pubblici e nei cloud degli enti pubblici degli Stati Uniti, ma non in altre aree cloud sovrane o governative di Azure.Advanced threat protection is available in all public clouds and US government clouds, but not in other sovereign or Azure government cloud regions.

Per informazioni dettagliate sui prezzi, inclusa una versione di valutazione gratuita di 30 giorni, vedere la pagina dei prezzi del Centro sicurezza di Azure.For pricing details, including a free 30 day [trial,](https://azure.microsoft.com/pricing/details/security-center/)see the Azure Security Center pricing page .


## <a name="set-up-advanced-threat-protection"></a>Configurare la protezione avanzata dalle minacce

È possibile configurare la protezione avanzata dalle minacce in diversi modi, descritto nelle sezioni seguenti.

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Avviare il [portale di Azure](https://portal.azure.com/).
1. Passare all'account di archiviazione di Azure.Navigate to your Azure Storage account. In **Impostazioni**selezionare **Sicurezza avanzata**.
1. Selezionare il collegamento **Impostazioni** nella pagina Configurazione avanzata della sicurezza.
1. Impostare **Sicurezza avanzata** **su ON**.
1. Fare clic su **Salva** per salvare il criterio nuovo o aggiornato.

    ![Attivare la protezione avanzata dalle minacce di Archiviazione di Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Centro sicurezza di Azure](#tab/azure-security-center)

Quando si sottoscrive il livello Standard nel Centro sicurezza di Azure, la protezione avanzata dalle minacce viene configurata automaticamente in tutti gli account di archiviazione. È possibile abilitare o disabilitare la protezione avanzata dalle minacce per gli account di archiviazione in una sottoscrizione specifica come segue:You can enable or disable advanced threat protection for your storage accounts under a specific subscription as follows:

1. Avviare il **Centro sicurezza di Azure** nel portale di [Azure.](https://portal.azure.com)
1. Dal menu principale, fai clic su **Impostazioni & prezzi**.
1. Fare clic sulla sottoscrizione che si vuole abilitare o disabilitare la protezione dalle minacce per i relativi account di archiviazione.

    ![Seleziona sottoscrizione](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Fare clic su **Piano prezzi**.
1. Nella sezione Selezionare il **piano tariffario per tipo di risorsa** fare clic su **Abilitato** o **Disabilitato**nella riga **Account di** archiviazione.

    ![Abilitare ATP nel Centro sicurezza](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Fare clic su **Salva**.

### <a name="template"></a>[Modello](#tab/template)

Usare un modello di Azure Resource Manager per distribuire un account di Archiviazione di Azure con la protezione avanzata dalle minacce abilitata. Per ulteriori informazioni, vedere [Account di archiviazione con protezione avanzata dalle minacce](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Criteri di Azure](#tab/azure-policy)

Usare un criterio di Azure per abilitare la protezione avanzata dalle minacce tra gli account di archiviazione in una sottoscrizione o un gruppo di risorse specifico.

1. Avviare la pagina Criteri di Azure **- Definizioni.Launch** the Azure Policy - Definitions page.

1. Cercare il criterio **Distribuisci Advanced Threat Protection in Account di archiviazione.**

     ![Criteri di ricerca](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Selezionare una sottoscrizione o un gruppo di risorse di Azure.Select an Azure subscription or resource group.

    ![Seleziona sottoscrizione o gruppo](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Assegnare il criterio.

    ![Pagina Definizioni dei criteri](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Usare i comandi dell'API di riposo per creare, aggiornare o ottenere l'impostazione di protezione avanzata dalle minacce per un account di archiviazione specifico.

* [Protezione avanzata dalle minacce - Crea](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Protezione avanzata dalle minacce - Ottenere](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilizzare i cmdlet di PowerShell seguenti:

* [Abilita protezione avanzata dalle minacce](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Ottieni protezione avanzata dalle minacce](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Disabilitare la protezione avanzata dalle minacce](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Esplorare le anomalie di sicurezza

Quando si verificano anomalie nelle attività di archiviazione, si riceve una e-mail di notifica con le informazioni sull'evento sospetto di sicurezza. I dettagli sull'evento comprendono:

* La natura dell'anomalia
* nome dell'account di archiviazione
* L'ora dell'evento
* Il tipo di storage
* Le cause potenziali
* Le fasi dell'indagine
* Le fasi di correzione

L'e-mail fornisce inoltre informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia.

![E-mail di avviso di protezione avanzata dalle minacce di Archiviazione di Microsoft Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

È possibile esaminare e gestire gli avvisi di sicurezza correnti dal [riquadro Avvisi](../../security-center/security-center-managing-and-responding-alerts.md)di sicurezza del Centro sicurezza di Azure. Facendo clic su un avviso specifico vengono visualizzati altri dettagli e azioni per analizzare la minaccia e risolvere eventuali minacce future.

![E-mail di avviso di protezione avanzata dalle minacce di Archiviazione di Microsoft Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Avvisi di sicurezza

Gli avvisi sono generati dai tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento degli account di archiviazione. Per un elenco di avvisi per Archiviazione di Azure, vedere la sezione Archiviazione in Protezione dalle minacce per i servizi dati nel Centro sicurezza di Azure.For a list of alerts for Azure Storage, see the **Storage** section in [Threat protection for data services in Azure Security Center.](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui log negli account di archiviazione di [AzureLearn](/rest/api/storageservices/About-Storage-Analytics-Logging) more about Logs in Azure Storage accounts
* Altre informazioni sul Centro sicurezza di [AzureLearn](../../security-center/security-center-intro.md) more about Azure Security Center
