---
title: Come risolvere gli errori di runtime U-SQL di Azure Data Lake Analytics
description: Informazioni su come risolvere gli errori di runtime U-SQL.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648454"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Informazioni su come risolvere gli errori di runtime U-SQL a causa di modifiche di runtime

Il runtime U-SQL di Azure Data Lake, inclusi il compilatore, l'ottimizzatore e il gestore di processi, è ciò che elabora il codice U-SQL.

## <a name="choosing-your-u-sql-runtime-version"></a>Scelta della versione di runtime di U-SQLChoosing your U-SQL runtime version

Quando si inviano processi U-SQL da Visual Studio, ADL SDK o dal portale di Azure Data Lake Analytics, il processo utilizzerà il runtime predefinito attualmente disponibile. Le nuove versioni del runtime U-SQL vengono rilasciate regolarmente e includono sia aggiornamenti minori che correzioni per la sicurezza.

È anche possibile scegliere una versione di runtime personalizzata; o perché si desidera provare un nuovo aggiornamento, è necessario rimanere su una versione precedente di un runtime o sono stati forniti con un hotfix per un problema segnalato in cui non è possibile attendere il nuovo aggiornamento regolare.

> [!CAUTION]
> La scelta di un runtime diverso da quello predefinito può interrompere i processi U-SQL. Utilizzare queste altre versioni solo per il test.

In rari casi, il supporto tecnico Microsoft può aggiungere una versione diversa di un runtime come predefinita per l'account. Assicurati di ripristinare questo pin il prima possibile. Se rimani bloccato a quella versione, scadrà in un secondo momento.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Monitoraggio della versione di runtime U-SQL dei processiMonitoring your jobs U-SQL runtime version

È possibile visualizzare la cronologia della versione di runtime usata dai processi precedenti nella cronologia dei processi dell'account tramite il browser dei processi di Visual Studio o la cronologia dei processi del portale di Azure.You can see the history of which runtime version your past jobs your past have used in your account's job history via the Visual Studio's job browser or the Azure portal's job history.

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Selezionare **Visualizza tutti i lavori**. Viene visualizzato un elenco di tutti i processi attivi e completati di recente nell'account.
3. Facoltativamente, fare clic su **Filtra** per trovare i processi in base ai valori di **Intervallo di tempo**, **Nome processo** e **Autore**.
4. È possibile visualizzare il runtime utilizzato nei processi completati.

![Visualizzazione della versione runtime di un processo precedente](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

Le versioni di runtime disponibili cambiano nel tempo. Il runtime predefinito è sempre chiamato "default" e manteniamo disponibile almeno il runtime precedente per un certo periodo di tempo, oltre a rendere disponibili runtime speciali per una serie di motivi. I runtime denominati in modo esplicito in genere seguono il formato seguente (il corsivo viene utilizzato per le parti variabili e [] indica parti facoltative):

release_YYYYMMDD_adl_buildno[_modifier]

Ad esempio, release_20190318_adl_3394512_2 indica la seconda versione della build 3394512 della versione runtime del 18 marzo 2019 e release_20190318_adl_3394512_private significa una build privata della stessa versione. Nota: La data è legata alla data in cui è stato effettuato l'ultimo check-in per tale rilascio e non necessariamente alla data di rilascio ufficiale.

Di seguito sono riportate le versioni di runtime attualmente disponibili.

- release_20190318_adl_3394512
- release_20190318_adl_5832669 l'impostazione predefinita corrente
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Risoluzione dei problemi relativi alla versione di runtime U-SQLTroubleshooting U-SQL runtime version issues

Esistono due possibili problemi di versione di runtime che possono verificarsi:There are two possible runtime version issues that you may encounter:

1. Uno script o un codice utente sta cambiando il comportamento da una release all'altra. Tali modifiche di rilievo vengono normalmente comunicate in anticipo con la pubblicazione delle note di rilascio. Se si verifica una modifica di rilievo, contattare il supporto tecnico Microsoft per segnalare questo comportamento di interruzione (nel caso in cui non sia stato ancora documentato) e inviare i processi rispetto alla versione di runtime precedente.

2. È stato utilizzato un runtime non predefinito in modo esplicito o implicito quando è stato aggiunto all'account e tale runtime è stato rimosso dopo qualche tempo. Se si verificano runtime mancanti, aggiornare gli script per l'esecuzione con il runtime predefinito corrente. Se hai bisogno di più tempo, contatta il supporto tecnico Microsoft

## <a name="see-also"></a>Vedere anche

- [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Gestire Azure Data Lake Analytics tramite il portale di AzureManage Azure Data Lake Analytics using Azure portal](data-lake-analytics-manage-use-portal.md)
- [Monitorare i processi in Azure Data Lake Analytics con il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
