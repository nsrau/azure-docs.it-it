---
title: Creare e ripristinare un backup in Servizi BizTalk | Documentazione Microsoft
description: "I Servizi BizTalk includono funzionalità di backup e ripristino. Informazioni su come creare e ripristinare un backup e determinare gli elementi di cui eseguire il backup. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 59f91173-4683-48df-abd5-41262bfce6df
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 45365092f5bcd1a8d309c10404a7437c494a8967
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2017
---
# <a name="biztalk-services-backup-and-restore"></a>Servizi BizTalk: backup e ripristino

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

I Servizi BizTalk di Azure includono funzioni di backup e ripristino. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!NOTE]
> Il backup delle connessioni ibride NON viene eseguito, indipendentemente dall'edizione. È necessario ricreare le connessioni ibride.


## <a name="before-you-begin"></a>Operazioni preliminari
* Le funzionalità di backup e ripristino potrebbero non essere disponibili per tutte le edizioni. Vedere [Servizi BizTalk: Grafico edizioni](biztalk-editions-feature-chart.md).
* È possibile ripristinare il contenuto del backup nello stesso servizio BizTalk oppure in nuovo Servizio BizTalk. Per ripristinare il Servizio BizTalk usando lo stesso nome è necessario eliminare il Servizio BizTalk esistente e il nome deve essere disponibile. Dopo aver eliminato un Servizio BizTalk, potrebbe essere necessario un certo periodo di tempo affinché lo stesso nome sia disponibile. Se non è possibile attendere che lo stesso nome sia disponibile, ripristinare un nuovo Servizio BizTalk.
* È possibile ripristinare la stessa edizione oppure un'edizione successiva dei Servizi BizTalk. Il ripristino di una versione precedente di Servizi BizTalk, risalente al momento in cui è stato creato il backup, non è supportato.
  
    Ad esempio, è possibile ripristinare un backup creato con l'edizione Basic nell'edizione Premium, ma non è possibile ripristinare un backup creato con l'edizione Premium nell'edizione Standard.
* Il backup dei numeri di controllo consente di mantenere la continuità dei numeri di controllo. Se dopo l'ultimo backup si elaborano dei messaggi, il ripristino del contenuto di questo backup può causare la generazione di numeri di controllo duplicati.
* Se un batch contiene messaggi attivi, elaborare il batch **prima** di eseguire un backup. Quando si crea un backup (secondo le esigenze o pianificato), i messaggi in batch non vengono mai archiviati. 
  
    **Se si crea un backup con la presenza di messaggi attivi in un batch, non verrà eseguito il backup di questi messaggi, che andranno pertanto perduti.**
* Facoltativo: nel portale di Servizi BizTalk arrestare qualsiasi operazione di gestione.

## <a name="create-a-backup"></a>Creare un backup
È possibile creare in qualsiasi momento un backup controllato interamente dall'utente. Per creare un backup, usare l'[API REST per la gestione dei servizi BizTalk in Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx).

## <a name="restore"></a>Restore
Per ripristinare un backup, usare l'[API REST per la gestione dei servizi BizTalk in Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx).

### <a name="postrestore"></a>Dopo aver ripristinato un backup
Lo stato di un servizio BizTalk ripristinato è sempre **Sospeso** . In questo stato è possibile apportare qualsiasi modifica alla configurazione prima che il nuovo ambiente diventi funzionale, ad esempio:

* Se le applicazioni del Servizio BizTalk sono state create mediante l'SDK di Servizi BizTalk di Azure, potrebbe essere necessario aggiornare le credenziali ACS in tali applicazioni, in modo che funzionino nell'ambiente ripristinato.
* Un servizio BizTalk viene ripristinato per replicare un ambiente di un servizio BizTalk esistente. In una situazione simile, se nel portale di Servizi BizTalk originale sono stati configurati contratti che usano una cartella FTP come origine, può essere necessario aggiornare tali contratti nell'ambiente appena ripristinato in modo da usare una cartella FTP di origine diversa. In caso contrario, è possibile che due diversi contratti tentino di eseguire il pull dello stesso messaggio.
* Se il ripristino è stato eseguito per ottenere più ambienti di servizi BizTalk, assicurarsi di individuare l'ambiente corretto mediante le applicazioni di Visual Studio, i cmdlet di PowerShell, le API REST oppure le API del modello a oggetti per la gestione dei partner commerciali.
* È buona norma configurare i backup automatizzati nell'ambiente del servizio BizTalk appena ripristinato.

## <a name="what-gets-backed-up"></a>Elementi di cui viene eseguito il backup
Viene eseguito il backup degli elementi seguenti:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Elementi di backup</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portale Servizi BizTalk di Azure</strong></td>
</tr> 
<tr>
<td>Configurazione e runtime</td> 
<td>
<ul>
<li>Dettagli su partner e profilo</li>
<li>Contratti con il partner</li>
<li>Assembly personalizzati distribuiti</li>
<li>Bridge distribuiti</li>
<li>Certificati</li>
<li>Trasformazioni distribuite</li>
<li>Pipeline</li>
<li>Modelli creati e salvati nel portale Servizi BizTalk</li>
<li>Mapping X12 ST01 e GS01</li>
<li>Numeri di controllo (EDI)</li>
<li>Valori MIC del messaggio AS2</li>
</ul>
</td>
</tr> 

<tr>
<td colspan="2">
 <strong>Servizio BizTalk di Azure</strong></td>
</tr> 
<tr>
<td>Certificato SSL</td> 
<td>
<ul>
<li>Dati certificato SSL</li>
<li>Password certificato SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Impostazioni del servizio BizTalk</td> 
<td>
<ul>
<li>Conteggio dell'unità di scala</li>
<li>Edizione</li>
<li>Versione prodotto</li>
<li>Area/data center</li>
<li>Spazio dei nomi e chiave del Servizio di controllo di accesso (ACS)</li>
<li>Rilevamento della stringa di connessione al database</li>
<li>Archiviazione della stringa di connessione all'account di archiviazione</li>
<li>Monitoraggio della stringa di connessione all'account di archiviazione</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Elementi aggiuntivi</strong></td>
</tr> 
<tr>
<td>Database di rilevamento</td> 
<td>Quando viene creato il servizio BizTalk, vengono immessi i dettagli relativi al database di rilevamento, tra cui il server di database SQL di Azure e il nome del database di rilevamento. Non viene eseguito il backup automatico del database di rilevamento.
<br/><br/>
<strong>Importante</strong><br/>
Se il database di rilevamento viene eliminato ed è necessario ripristinarlo, è necessario disporre di un backup precedente. Se non è disponibile un backup, non sarà possibile recuperare il database di rilevamento e i relativi dati. In questo caso, creare un nuovo database di rilevamento con lo stesso nome. È consigliata la replica geografica.</td>
</tr> 
</table>

## <a name="next"></a>Avanti
Per creare i servizi BizTalk di Azure, passare a [Servizi BizTalk: Provisioning ](http://go.microsoft.com/fwlink/p/?LinkID=302280). Per iniziare a creare applicazioni, vedere [Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Vedere anche
* [Backup del servizio BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Ripristino del servizio BizTalk da un backup](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [Servizi BizTalk: Grafico edizioni Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [Servizi BizTalk: Provisioning](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [Servizi BizTalk: Tabella degli stati del servizio](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [Servizi BizTalk: Schede Dashboard, Monitoraggio, Scalabilità](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [Servizi BizTalk: limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [Servizi BizTalk: nome e chiave dell'autorità emittente](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Come iniziare a usare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png

