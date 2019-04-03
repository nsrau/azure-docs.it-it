---
title: Linee guida per il ripristino di emergenza in Azure Data Lake Storage Gen1 | Microsoft Docs
description: Linee guida per il ripristino di emergenza in Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: b3f1888a73baf2b7f9efa9f5e7cdb3305aa9f90d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878112"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Materiale sussidiario per il ripristino di emergenza dei dati in Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 offre l'archiviazione con ridondanza locale (LRS). I dati nell'account Data Lake Storage Gen1 sono pertanto resilienti agli errori hardware temporanei di un data center grazie all'uso di repliche automatizzate. Ciò garantisce durabilità e disponibilità elevata, rispettando il Contratto di servizio di Data Lake Storage Gen1. Questo articolo fornisce alcune linee guida su come proteggere ulteriormente i dati dalle rare interruzioni a livello di area o da eliminazioni accidentali.

## <a name="disaster-recovery-guidance"></a>Indicazioni sul ripristino di emergenza
È fondamentale che ogni cliente prepari il proprio piano di ripristino di emergenza. Leggere le informazioni contenute in questo articolo per creare il proprio piano di ripristino di emergenza. Di seguito sono riportate alcune risorse che consentono di creare proprio piano.

* [Ripristino di emergenza e disponibilità elevata per le applicazioni Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Indicazioni tecniche sulla resilienza di Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Procedure consigliate
È consigliabile copiare i dati critici in un altro account Data Lake Storage Gen1 in un'altra area con una frequenza adatta alle esigenze del piano di ripristino di emergenza. Sono disponibili diversi metodi per copiare i dati, tra cui [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) o [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory è un servizio utile per creare e distribuire pipeline di spostamento dati a intervalli ricorrenti.

Se si verifica un'interruzione del servizio nell'area, è quindi possibile accedere ai dati nell'area in cui sono stati copiati. È possibile monitorare il [dashboard di integrità dei servizi di Azure](https://azure.microsoft.com/status/) per individuare lo stato dei servizi di Azure in tutto il mondo.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Indicazioni per il ripristino dal danneggiamento o dall'eliminazione accidentale dei dati
Anche se Data Lake Storage Gen1 offre la resilienza dei dati tramite le repliche automatiche, ciò non impedisce all'applicazione o agli sviluppatori/utenti di danneggiare i dati o di eliminarli accidentalmente.

### <a name="best-practices"></a>Procedure consigliate
Per evitare l'eliminazione accidentale, è consigliabile impostare prima di tutto i criteri di accesso corretti per l'account Data Lake Storage Gen1.  Ciò include l'applicazione dei [blocchi di risorse di Azure](../azure-resource-manager/resource-group-lock-resources.md) per bloccare le risorse importanti e l'applicazione del controllo di accesso a livello di account e file usando le [funzionalità di sicurezza di Data Lake Storage Gen1](data-lake-store-security-overview.md) disponibili. È anche opportuno creare regolarmente copie dei dati critici usando [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) o [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) in un altro account Data Lake Storage Gen1, in una cartella o in una sottoscrizione di Azure.  Queste copie possono essere usate per il ripristino in seguito a un danneggiamento dei dati o un evento imprevisto di eliminazione. Azure Data Factory è un servizio utile per creare e distribuire pipeline di spostamento dati a intervalli ricorrenti.

Le organizzazioni possono anche abilitare la [registrazione diagnostica](data-lake-store-diagnostic-logs.md) per l'account Data Lake Storage Gen1 per raccogliere gli audit trail di accesso ai dati che forniscono informazioni su chi potrebbe avere eliminato o aggiornato un file.

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione a Azure Data Lake Store Gen1](data-lake-store-get-started-portal.md)
* [Proteggere i dati in Data Lake archiviazione Gen1](data-lake-store-secure-data.md)

