---
title: Linee guida per il ripristino di emergenza in Azure Data Lake Storage Gen1 | Microsoft Docs
description: Informazioni su come proteggere ulteriormente i dati dalle interruzioni a livello di area o da eliminazioni accidentali oltre l'archiviazione con ridondanza locale di Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: b124f828c4a6a019c45243528ed2d957e3f781f3
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88191414"
---
# <a name="high-availability-and-disaster-recovery-guidance-for-data-lake-storage-gen1"></a>Guida alla disponibilità elevata e al ripristino di emergenza per Data Lake Storage Gen1

Data Lake Storage Gen1 fornisce l'archiviazione con ridondanza locale (con ridondanza locale). Pertanto, i dati nell'account di Data Lake Storage Gen1 sono resilienti agli errori hardware temporanei all'interno di un Data Center tramite le repliche automatiche. Ciò garantisce durabilità e disponibilità elevata, rispettando il Contratto di servizio di Data Lake Storage Gen1. Questo articolo fornisce alcune linee guida su come proteggere ulteriormente i dati dalle rare interruzioni a livello di area o da eliminazioni accidentali.

## <a name="disaster-recovery-guidance"></a>Indicazioni sul ripristino di emergenza

È essenziale preparare un piano di ripristino di emergenza. Esaminare le informazioni contenute in questo articolo e queste risorse aggiuntive per creare un piano personalizzato.

* [Ripristino di emergenza e disponibilità elevata per le applicazioni Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Indicazioni tecniche sulla resilienza di Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practice-recommendations"></a>Indicazioni sulle procedure consigliate

È consigliabile copiare i dati critici in un altro account Data Lake Storage Gen1 in un'altra area con una frequenza adatta alle esigenze del piano di ripristino di emergenza. Sono disponibili diversi metodi per copiare i dati, tra cui [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md)o [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory è un servizio utile per creare e distribuire pipeline di spostamento dati a intervalli ricorrenti.

Se si verifica un'interruzione del servizio nell'area, è quindi possibile accedere ai dati nell'area in cui sono stati copiati. È possibile monitorare il [dashboard di integrità dei servizi di Azure](https://azure.microsoft.com/status/) per individuare lo stato dei servizi di Azure in tutto il mondo.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Indicazioni per il ripristino dal danneggiamento o dall'eliminazione accidentale dei dati

Anche se Data Lake Storage Gen1 offre la resilienza dei dati tramite le repliche automatiche, ciò non impedisce all'applicazione o agli sviluppatori/utenti di danneggiare i dati o di eliminarli accidentalmente.

Per evitare l'eliminazione accidentale, è consigliabile impostare prima di tutto i criteri di accesso corretti per l'account Data Lake Storage Gen1. Questo include l'applicazione dei [blocchi delle risorse di Azure](../azure-resource-manager/management/lock-resources.md) per bloccare le risorse importanti e l'applicazione del controllo di accesso a livello di file e di account usando le [funzionalità di sicurezza data Lake storage Gen1](data-lake-store-security-overview.md)disponibili. È anche opportuno creare regolarmente copie dei dati critici usando [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) o [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) in un altro account Data Lake Storage Gen1, in una cartella o in una sottoscrizione di Azure. Queste copie possono essere usate per il ripristino in seguito a un danneggiamento dei dati o un evento imprevisto di eliminazione. Azure Data Factory è un servizio utile per creare e distribuire pipeline di spostamento dati a intervalli ricorrenti.

È anche possibile abilitare la [registrazione diagnostica](data-lake-store-diagnostic-logs.md) per un account data Lake storage Gen1 per raccogliere gli itinerari di controllo dell'accesso ai dati. I percorsi di controllo forniscono informazioni su chi potrebbe avere eliminato o aggiornato un file.

È possibile provare a ripristinare un elemento eliminato usando il modulo [AZ. archivio data lake](https://docs.microsoft.com/powershell/module/az.datalakestore/) Azure PowerShell per data Lake storage gen 1. In particolare, vedere il comando [Restore-AzDataLakeStoreDeletedItem](https://docs.microsoft.com/powershell/module/az.datalakestore/restore-azdatalakestoredeleteditem) . Assicurarsi di esaminare la sezione [Descrizione](https://docs.microsoft.com/powershell/module/az.datalakestore/restore-azdatalakestoredeleteditem#description) prima di provare a usare questo comando.

## <a name="next-steps"></a>Passaggi successivi

* [Iniziare a usare Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)
