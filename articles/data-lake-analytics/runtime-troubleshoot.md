---
title: Come risolvere gli errori di runtime di U-SQL Azure Data Lake Analytics
description: Informazioni su come risolvere gli errori di runtime di U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.openlocfilehash: 41b7c80c85331f288343351749e6b2e5292b30c6
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241608"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Informazioni su come risolvere gli errori di runtime di U-SQL a causa di modifiche di runtime

Il runtime U-SQL di Azure Data Lake, tra cui compilatore, ottimizzatore e gestore di processi, si occupa di elaborare il codice U-SQL.

## <a name="choosing-your-u-sql-runtime-version"></a>Scelta della versione del runtime U-SQL

Quando si inviano processi U-SQL da Visual Studio, ADL SDK o il portale di Azure Data Lake Analytics, il processo userà il runtime predefinito attualmente disponibile. Le nuove versioni del runtime U-SQL vengono rilasciate a intervalli regolari e includono aggiornamenti secondari e correzioni per la sicurezza.

È anche possibile scegliere una versione runtime personalizzata. perché si vuole provare un nuovo aggiornamento, è necessario rimanere in una versione precedente di un Runtime oppure è stato fornito un hotfix per un problema segnalato in cui non è possibile attendere il normale nuovo aggiornamento.

> [!CAUTION]
> La scelta di un runtime diverso da quello predefinito potrebbe interrompere i processi U-SQL. Usare queste altre versioni solo per i test.

In rari casi, supporto tecnico Microsoft possibile aggiungere una versione diversa di un runtime come impostazione predefinita per l'account. Assicurarsi di ripristinare questo pin il prima possibile. Se si rimane bloccati a tale versione, la scadenza scadrà in un secondo momento.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Monitoraggio dei processi versione runtime U-SQL

È possibile visualizzare la cronologia della versione runtime usata dai processi precedenti nella cronologia processo dell'account tramite il browser processi di Visual Studio o la cronologia processi del portale di Azure.

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Selezionare **Visualizza tutti i processi**. Viene visualizzato un elenco di tutti i processi attivi e completati di recente nell'account.
3. Facoltativamente, fare clic su **Filtra** per trovare i processi in base ai valori di **Intervallo di tempo**, **Nome processo** e **Autore**.
4. È possibile visualizzare il runtime usato nei processi completati.

![Visualizzazione della versione runtime di un processo passato](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

Le versioni di runtime disponibili cambiano nel tempo. Il runtime predefinito viene sempre denominato "default" e si mantiene disponibile almeno il runtime precedente per un certo periodo di tempo, oltre a rendere disponibili speciali Runtime per diversi motivi. I runtime denominati in modo esplicito seguono il formato seguente (i corsivo vengono usati per le parti variabili e [] indica le parti facoltative):

release_YYYYMMDD_adl_buildno [_modifier]

Ad esempio, release_20190318_adl_3394512_2 significa che la seconda versione della build 3394512 della versione di runtime di marzo 18 2019 e release_20190318_adl_3394512_private significa una compilazione privata della stessa versione. Nota: la data è correlata al momento in cui è stata eseguita l'ultima archiviazione per tale versione e non necessariamente alla data di rilascio ufficiale.


## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Risoluzione dei problemi relativi alla versione di runtime di U-SQL

Esistono due possibili problemi di versione di runtime che possono verificarsi:

1. Uno script o codice utente sta cambiando il comportamento da una versione a quella successiva. Tali modifiche di rilievo vengono in genere comunicate in anticipo con la pubblicazione delle note sulla versione. Se si verifica una modifica di rilievo di questo tipo, contattare supporto tecnico Microsoft per segnalare questo comportamento di suddivisione (nel caso in cui non sia ancora stato documentato) e inviare i processi alla versione di runtime precedente.

2. Si è usato un runtime non predefinito in modo esplicito o implicito quando è stato aggiunto all'account e tale Runtime è stato rimosso in un secondo momento. Se si verificano runtime mancanti, aggiornare gli script per l'esecuzione con il runtime predefinito corrente. Se è necessario ulteriore tempo, contattare supporto tecnico Microsoft

## <a name="known-issues"></a>Problemi noti

* Se si fa riferimento Newtonsoft.Jsalla versione del file 12.0.3 o in uno script USQL, si verificherà un errore di compilazione seguente:

    *"Ci dispiace. i processi in esecuzione nell'account di Data Lake Analytics probabilmente verranno eseguiti più lentamente o non verranno completati. Un problema imprevisto impedisce il ripristino automatico di questa funzionalità nell'account Azure Data Lake Analytics. I tecnici Azure Data Lake sono stati contattati per esaminare ".*  

    Dove lo stack di chiamate conterrà:  
    `System.IndexOutOfRangeException: Index was outside the bounds of the array.`  
    `at Roslyn.Compilers.MetadataReader.PEFile.CustomAttributeTableReader.get_Item(UInt32 rowId)`  
    `...`

    **Soluzione**: usare Newtonsoft.Jsnel file 12.0.2 o versione precedente.


## <a name="see-also"></a>Vedi anche

- [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Gestire Azure Data Lake Analytics tramite portale di Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorare i processi in Azure Data Lake Analytics con il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
