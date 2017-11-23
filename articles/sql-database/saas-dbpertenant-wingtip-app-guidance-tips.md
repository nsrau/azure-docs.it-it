---
title: Materiale sussidiario per l'esempio di app multi-tenant di database SQL - SaaS Wingtip | Microsoft Docs
description: Fornisce i passaggi e il materiale sussidiario per l'installazione e l'esecuzione dell'applicazione multi-tenant di esempio che usa Database SQL di Azure, nell'esempio Wingtip SaaS.
keywords: esercitazione database SQL
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: genemi
ms.openlocfilehash: 4c90d70bb3b043ef81a224f0f69107eaa6eb0547
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="guidance-and-tips-for-azure-sql-database-multi-tenant-saas-app-example"></a>Materiale sussidiario e suggerimenti per l'esempio di app SaaS multi-tenant di database SQL di Azure


## <a name="download-and-unblock-the-wingtip-tickets-saas-database-per-tenant-scripts"></a>Scaricare e sbloccare gli script per l'applicazione SaaS di database per tenant Wingtip Tickets

I contenuti eseguibili (script, DLL) possono essere bloccati da Windows quando si scaricano e si estraggono i file ZIP da un'origine esterna. Quando si estraggono gli script da un file ZIP, ***seguire questa procedura per sbloccare il file ZIP prima di estrarlo***. In questo modo sarà possibile eseguire gli script.

1. Passare al [repository GitHub per l'applicazione SaaS di database per tenant Wingtip Tickets](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
2. Fare clic su **Clona o scarica**.
3. Fare clic su **Scarica ZIP** e salvare il file.
4. Fare clic con il pulsante destro del mouse sul file **WingtipTicketsSaaS-DbPerTenant-master.zip** e scegliere **Proprietà**.
5. Nella scheda **Generale** selezionare **Annulla blocco**.
6. Fare clic su **OK**.
7. Estrarre i file.

Gli script si trovano nella cartella *..\\Learning Modules*.


## <a name="working-with-the-wingtip-tickets-saas-database-per-tenant-powershell-scripts"></a>Uso degli script di PowerShell per l'applicazione SaaS di database per tenant Wingtip Tickets

Per ottenere il massimo dall'esempio, è necessario esplorare gli script forniti. Usare i punti di interruzione e scorrere gli script, esaminando in dettaglio come vengono implementati i diversi schemi SaaS. Per scorrere facilmente gli script e i moduli forniti e comprenderli a fondo, è consigliabile usare [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Aggiornare il file di configurazione per la distribuzione

Modificare il file **UserConfig.psm1** con il valore per il gruppo di risorse e per l'utente usato durante la distribuzione:

1. Aprire *PowerShell ISE* e caricare ...\\Learning Modules\\*UserConfig.psm1* 
2. Aggiornare *ResourceGroupName* e *Name* con i valori specifici della distribuzione in uso (solo alle righe 10 e 11).
3. Salvare le modifiche.

Impostando qui questi valori specifici della distribuzione, non sarà necessario aggiornarli in ogni script.

### <a name="execute-scripts-by-pressing-f5"></a>Eseguire gli script premendo F5

Vari script usano *$PSScriptRoot* per esplorare le cartelle e *$PSScriptRoot* viene valutato solo quando gli script vengono eseguiti premendo **F5**.  L'evidenziazione e l'esecuzione di una selezione (**F8**) può causare errori, quindi premere **F5** per l'esecuzione degli script.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Eseguire gli script un'istruzione alla volta per esaminare l'implementazione

Il modo migliore di comprendere gli script è di scorrerli per capirne la funzione. Esaminare gli script **Demo-** inclusi che presentano un flusso di lavoro generale facilmente interpretabile. Gli script **Demo-** illustrano la procedura necessaria per eseguire ogni attività, quindi impostare punti di interruzione e analizzare in modo più approfondito le singole chiamate per vedere i dettagli dell'implementazione per i diversi schemi SaaS.

Suggerimenti per esplorare e scorrere gli script PowerShell:

- Aprire gli script **Demo-** in PowerShell ISE.
- Eseguire o continuare con **F5**. Non è consigliabile usare **F8** perché la variabile *$PSScriptRoot* non viene valutata durante l'esecuzione di selezioni di uno script.
- Aggiungere punti di interruzione facendo clic o selezionando una riga e premendo **F9**.
- Eseguire una funzione o una chiamata dello script in blocco con **F10** (comando Esegui istruzione/routine).
- Eseguire una funzione o una chiamata dello script un'istruzione alla volta con **F11**.
- Uscire dalla funzione o dalla chiamata dello script corrente con **MAIUSC+F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Esplorare lo schema del database ed eseguire query SQL tramite SSMS

Usare [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) per connettersi ai server e ai database dell'applicazione e per esplorarli.

La distribuzione inizialmente ha due server di database SQL a cui connettersi: il server *tenants1-dpt-&lt;Utente&gt;* e il server *catalog-dpt-&lt;Utente&gt;*. Per assicurare una connessione demo corretta, entrambi i server hanno una [regola del firewall](sql-database-firewall-configure.md) che consente tutti gli IP.


1. Aprire *SSMS* e connettersi al server *tenants1-dpt-&lt;Utente&gt;.database.windows.net*.
2. Fare clic su **Connetti** > **Motore di database...**:

   ![server di catalogo](media/saas-dbpertenant-wingtip-app-guidance-tips/connect.png)

3. Le credenziali per la demo sono: Account di accesso = *developer*, Password = *P@ssword1*

   ![connessione](media/saas-dbpertenant-wingtip-app-guidance-tips/tenants1-connect.png)

4. Ripetere i passaggi 2 e 3 e connettersi al server *catalog-dpt-&lt;Utente&gt;.database.windows.net*.


Dopo avere stabilito la connessione, entrambi i server dovrebbero essere visibili. L'elenco dei propri database potrebbe variare a seconda dei tenant di cui si è effettuato il provisioning.

![esplora oggetti](media/saas-dbpertenant-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Passaggi successivi

[Distribuire l'applicazione SaaS di database per tenant Wingtip Tickets](saas-dbpertenant-get-started-deploy.md)

