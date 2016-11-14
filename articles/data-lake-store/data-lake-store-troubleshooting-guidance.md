---
title: Domande frequenti su Azure Data Lake Store | Documentazione Microsoft
description: Guida alla risoluzione dei problemi o ai problemi di migrazione con Azure Data Lake Store
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf7fd555-3e30-43ce-b28c-c3ad0a241fdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 84d644e07036f511e685b0fd020cdf8c310fd969


---
# <a name="frequently-asked-questions-for-azure-data-lake-store"></a>Domande frequenti su Azure Data Lake Store
Questo articolo contiene le domande frequenti relative ad Azure Data Lake Store.

## <a name="how-do-i-handle-disaster-recovery-and-data-corruption-for-my-azure-data-lake-store-account"></a>Come si gestiscono il ripristino di emergenza e il danneggiamento dei dati per l'account Azure Data Lake Store?
I dati nell'account Azure Data Lake Store sono resilienti agli errori hardware temporanei di un'area grazie alle repliche automatizzate. Ciò garantisce durabilità e disponibilità elevata, rispettando il Contratto di servizio di Azure Data Lake Store. Ecco alcune indicazioni su come proteggere ulteriormente i dati dalle rare interruzioni a livello di area o da eliminazioni accidentali.

### <a name="disaster-recovery-guidance"></a>Indicazioni sul ripristino di emergenza
È fondamentale che ogni cliente prepari il proprio piano di ripristino di emergenza. Per creare un piano di ripristino di emergenza, vedere la documentazione di Azure seguente. Di seguito sono riportate alcune risorse che consentono di creare proprio piano.

* [Ripristino di emergenza e disponibilità elevata per le applicazioni Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Indicazioni tecniche sulla resilienza di Azure](../resiliency/resiliency-technical-guidance.md)

#### <a name="best-practices"></a>Procedure consigliate
È consigliabile copiare i dati critici in un altro account Data Lake Store in un'altra area con una frequenza adatta alle esigenze del piano di ripristino di emergenza. Sono disponibili diversi metodi per copiare i dati, tra cui [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) o [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md). Azure Data Factory è un servizio utile per creare e distribuire pipeline di spostamento dati a intervalli ricorrenti.

Se verifica un'interruzione a livello di area, sarà quindi possibile accedere ai dati nell'area in cui sono stati copiati. È possibile monitorare il [dashboard dell'integrità dei servizi di Azure](https://azure.microsoft.com/status/) per determinare lo stato dei servizi di Azure in tutto il mondo.

### <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Indicazioni per il ripristino dal danneggiamento o dall'eliminazione accidentale dei dati
Anche se Azure Data Lake Store offre la resilienza dei dati tramite le repliche automatiche, ciò non impedisce all'applicazione o agli sviluppatori/utenti di danneggiare i dati o di eliminarli accidentalmente.

#### <a name="best-practices"></a>Procedure consigliate
Per evitare l'eliminazione accidentale, è consigliabile impostare prima i criteri di accesso corretti per l'account Data Lake Store usando le [funzionalità di sicurezza di Data Lake Store](data-lake-store-security-overview.md) disponibili. È anche opportuno creare regolarmente copie dei dati critici usando [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) o [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md) in un altro account Data Lake Store, in una cartella o in una sottoscrizione di Azure.  Queste copie possono essere usate per il ripristino in seguito a un danneggiamento dei dati o un evento imprevisto di eliminazione. Azure Data Factory è un servizio utile per creare e distribuire pipeline di spostamento dati a intervalli ricorrenti.

Le organizzazioni possono anche abilitare la [registrazione diagnostica](data-lake-store-diagnostic-logs.md) per l'account Azure Data Lake Store per raccogliere gli audit trail di accesso ai dati che forniscono informazioni su chi potrebbe avere eliminato o aggiornato un file.

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Azure Data Lake Store](data-lake-store-get-started-portal.md)
* [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)




<!--HONumber=Nov16_HO2-->


