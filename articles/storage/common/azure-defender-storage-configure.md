---
title: Configurare Azure Defender per l'archiviazione
titleSuffix: Azure Storage
description: Configurare Azure Defender per l'archiviazione per rilevare le anomalie nell'attività dell'account e ricevere notifiche relative a tentativi potenzialmente dannosi di accesso all'account.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: eb71c5eda66d3d11385577da7142970f4fbcbad2
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92474853"
---
# <a name="configure-azure-defender-for-storage"></a>Configurare Azure Defender per l'archiviazione

Azure Defender per l'archiviazione offre un ulteriore livello di intelligence per la sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit degli account di archiviazione. Questo livello di protezione consente di risolvere le minacce senza essere un esperto della sicurezza o gestire sistemi di monitoraggio della sicurezza.

Gli avvisi di sicurezza vengono attivati quando si verifica un'anomalia nell'attività. Questi avvisi di sicurezza sono integrati con il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/)e vengono inviati anche tramite posta elettronica agli amministratori della sottoscrizione, con informazioni dettagliate sulle attività sospette e consigli su come analizzare e correggere le minacce.

Il servizio inserisce i log delle risorse di richieste di lettura, scrittura ed eliminazione nell'archivio BLOB e in File di Azure per il rilevamento delle minacce. Per esaminare gli avvisi di Azure Defender, è possibile visualizzare le attività di archiviazione correlate usando Analisi archiviazione registrazione. Per altre informazioni, vedere **configurare la registrazione** in [monitorare un account di archiviazione nell'portale di Azure](storage-monitor-storage-account.md#configure-logging).

## <a name="availability"></a>Disponibilità

Azure Defender per l'archiviazione è attualmente disponibile per l'archiviazione BLOB, File di Azure e Azure Data Lake Storage Gen2. I tipi di account che supportano Azure Defender includono gli account per utilizzo generico V2, BLOB in blocchi e archiviazione BLOB. Azure Defender per l'archiviazione è disponibile in tutti i cloud pubblici e negli Stati Uniti, ma non in altre aree del cloud di Azure per enti pubblici o sovrani.

Gli account con spazi dei nomi gerarchici abilitati per Data Lake Storage supportano le transazioni usando le API di archiviazione BLOB di Azure e le API di Data Lake Storage. Le condivisioni file di Azure supportano le transazioni tramite SMB.

Per informazioni dettagliate sui prezzi, inclusa una versione di valutazione gratuita di 30 giorni, vedere la [pagina dei prezzi del Centro sicurezza di Azure](https://azure.microsoft.com/pricing/details/security-center/).

Nell'elenco seguente viene riepilogata la disponibilità di Azure Defender per l'archiviazione:

- Stato della versione:
  - [Archiviazione BLOB](https://azure.microsoft.com/services/storage/blobs/) (disponibilità generale)
  - [File di Azure](/azure/storage/files/storage-files-introduction) (disponibilità generale)
  - Azure Data Lake Storage Gen2 (disponibilità generale)
- Cloud:<br>
    ✔ Cloud commerciali<br>
    ✔ US Gov<br>
    ✘ Cina, altro gov

## <a name="set-up-azure-defender"></a>Configurare Azure Defender

È possibile configurare Azure Defender per l'archiviazione in diversi modi, descritti nelle sezioni seguenti.

### <a name="azure-security-center"></a>[Centro sicurezza di Azure](#tab/azure-security-center)

Quando si sottoscrive il livello standard nel centro sicurezza di Azure, Azure Defender viene impostato automaticamente su tutti gli account di archiviazione. È possibile abilitare o disabilitare Azure Defender per gli account di archiviazione in una sottoscrizione specifica, come indicato di seguito:

1. Avviare il **Centro sicurezza di Azure** nella [portale di Azure](https://portal.azure.com).
1. Dal menu principale, in **gestione**, selezionare **prezzi & impostazioni**.
1. Selezionare la sottoscrizione per cui si vuole abilitare o disabilitare Azure Defender.
1. Selezionare **Azure Defender in** per abilitare Azure Defender per la sottoscrizione.
1. In **selezione piano di Azure Defender per tipo di risorsa**individuare la riga di **archiviazione** e selezionare **abilitata** nella colonna **piano** .
1. Salvare le modifiche.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Screenshot che illustra come abilitare Azure Defender per l'archiviazione nel centro sicurezza":::

Azure Defender è ora abilitato per tutti gli account di archiviazione in questa sottoscrizione.

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Avviare il [portale di Azure](https://portal.azure.com/).
1. Passare all'account di archiviazione. In **Impostazioni**selezionare **sicurezza avanzata**.
1. Selezionare **Abilita Azure Defender per l'archiviazione**.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Screenshot che illustra come abilitare Azure Defender per l'archiviazione nel centro sicurezza":::

Azure Defender è ora abilitato per questo account di archiviazione.

### <a name="template"></a>[Modello](#tab/template)

Usare un modello di Azure Resource Manager per distribuire un account di archiviazione di Azure con Azure Defender abilitato. Per altre informazioni, vedere [account di archiviazione con Advanced Threat Protection](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Criteri di Azure](#tab/azure-policy)

Usare un criterio di Azure per abilitare Azure Defender tra gli account di archiviazione in una sottoscrizione o un gruppo di risorse specifico.

1. Avviare la pagina **criteri di Azure-definizioni** .
1. Cercare i criteri **deploy Azure Defender on Storage Accounts** .

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Screenshot che illustra come abilitare Azure Defender per l'archiviazione nel centro sicurezza":::

1. Selezionare una sottoscrizione o un gruppo di risorse di Azure.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Screenshot che illustra come abilitare Azure Defender per l'archiviazione nel centro sicurezza":::

1. Assegnare i criteri.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Screenshot che illustra come abilitare Azure Defender per l'archiviazione nel centro sicurezza":::

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Usare i comandi dell'API REST per creare, aggiornare o ottenere l'impostazione di Azure Defender per un account di archiviazione specifico.

- [Advanced Threat Protection-crea](/rest/api/securitycenter/advancedthreatprotection/create)
- [Advanced Threat Protection-Ottieni](/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Usare i cmdlet di PowerShell seguenti:

- [Abilita Advanced Threat Protection](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
- [Ottenere Advanced Threat Protection](/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
- [Disabilitare Advanced Threat Protection](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Esplorare le anomalie di sicurezza

Quando si verificano anomalie nelle attività di archiviazione, si riceve una e-mail di notifica con le informazioni sull'evento sospetto di sicurezza. I dettagli sull'evento comprendono:

- Natura dell'anomalia
- Il nome dell'account di archiviazione
- Ora dell'evento
- Tipo di archiviazione
- Possibili cause
- Passaggi dell'indagine
- Procedura di correzione

L'e-mail fornisce inoltre informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Screenshot che illustra come abilitare Azure Defender per l'archiviazione nel centro sicurezza":::

È possibile esaminare e gestire gli avvisi di sicurezza correnti dal [riquadro avvisi di sicurezza](../../security-center/security-center-managing-and-responding-alerts.md)del Centro sicurezza di Azure. Facendo clic su un avviso specifico vengono visualizzati altri dettagli e azioni per analizzare la minaccia e risolvere eventuali minacce future.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Screenshot che illustra come abilitare Azure Defender per l'archiviazione nel centro sicurezza":::

## <a name="security-alerts"></a>Avvisi di sicurezza

Gli avvisi sono generati dai tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento degli account di archiviazione. Per un elenco degli avvisi per archiviazione di Azure, vedere [avvisi per archiviazione di Azure](../../security-center/alerts-reference.md#alerts-azurestorage).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [log negli account di archiviazione di Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)
- Altre informazioni sul [Centro sicurezza di Azure](../../security-center/security-center-intro.md)
