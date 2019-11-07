---
title: Come risolvere gli errori di runtime di U-SQL Azure Data Lake Analytics
description: Informazioni su come risolvere gli errori di runtime di U-SQL.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648454"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Informazioni su come risolvere gli errori di runtime di U-SQL a causa di modifiche di runtime

Il Azure Data Lake Runtime U-SQL, inclusi il compilatore, l'ottimizzatore e il gestore di processi, è il processo che elabora il codice U-SQL.

## <a name="choosing-your-u-sql-runtime-version"></a>Scelta della versione del runtime U-SQL

Quando si inviano processi U-SQL da Visual Studio, ADL SDK o il portale di Azure Data Lake Analytics, il processo userà il runtime predefinito attualmente disponibile. Le nuove versioni del runtime U-SQL vengono rilasciate a intervalli regolari e includono aggiornamenti secondari e correzioni per la sicurezza.

È anche possibile scegliere una versione runtime personalizzata. perché si vuole provare un nuovo aggiornamento, è necessario rimanere in una versione precedente di un Runtime oppure è stato fornito un hotfix per un problema segnalato in cui non è possibile attendere il normale nuovo aggiornamento.

> [!CAUTION]
> La scelta di un runtime diverso da quello predefinito può causare l'interruzione dei processi U-SQL. Usare queste altre versioni solo per i test.

In rari casi, supporto tecnico Microsoft possibile aggiungere una versione diversa di un runtime come impostazione predefinita per l'account. Assicurarsi di ripristinare questo pin il prima possibile. Se si rimane bloccati a tale versione, la scadenza scadrà in un secondo momento.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Monitoraggio dei processi versione runtime U-SQL

È possibile visualizzare la cronologia della versione runtime usata dai processi precedenti nella cronologia processo dell'account tramite il browser processi di Visual Studio o la cronologia processi del portale di Azure.

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Selezionare **Visualizza tutti i processi**. Viene visualizzato un elenco di tutti i processi attivi e completati di recente nell'account.
3. Facoltativamente, fare clic su **Filtra** per trovare i processi in base ai valori di **Intervallo di tempo**, **Nome processo** e **Autore**.
4. È possibile visualizzare il runtime usato nei processi completati.

![Visualizzazione della versione runtime di un processo passato](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

Le versioni di runtime disponibili cambiano nel tempo. Il runtime predefinito viene sempre denominato "default" e si mantiene disponibile almeno il runtime precedente per un certo periodo di tempo, oltre a rendere disponibili speciali Runtime per diversi motivi. I runtime denominati in modo esplicito seguono il formato seguente (i corsivo vengono usati per le parti variabili e [] indica le parti facoltative):

release_YYYYMMDD_adl_buildno[_modifier]

Ad esempio, release_20190318_adl_3394512_2 indica la seconda versione della build 3394512 della versione di runtime di March 18 2019 e release_20190318_adl_3394512_private indica una compilazione privata della stessa versione. Nota: la data è correlata al momento in cui è stata eseguita l'ultima archiviazione per tale versione e non necessariamente alla data di rilascio ufficiale.

Di seguito sono riportate le versioni di Runtime attualmente disponibili.

- release_20190318_adl_3394512
- release_20190318_adl_5832669 l'impostazione predefinita corrente
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Risoluzione dei problemi relativi alla versione di runtime di U-SQL

Esistono due possibili problemi di versione di runtime che possono verificarsi:

1. Uno script o codice utente sta cambiando il comportamento da una versione a quella successiva. Tali modifiche di rilievo vengono in genere comunicate in anticipo con la pubblicazione delle note sulla versione. Se si verifica una modifica di rilievo di questo tipo, contattare supporto tecnico Microsoft per segnalare questo comportamento di suddivisione (nel caso in cui non sia ancora stato documentato) e inviare i processi alla versione precedente del runtime.

2. Si è usato un runtime non predefinito in modo esplicito o implicito quando è stato aggiunto all'account e tale Runtime è stato rimosso in un secondo momento. Se si verificano runtime mancanti, aggiornare gli script per l'esecuzione con il runtime predefinito corrente. Se è necessario ulteriore tempo, contattare supporto tecnico Microsoft

## <a name="see-also"></a>Vedere anche

- [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorare i processi in Azure Data Lake Analytics usando il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
