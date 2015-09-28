<properties
   pageTitle="Tabelle di Azure Backup limiti"
   description="Descrive i limiti di sistema per il Backup di Azure."
   services="backup"
   documentationCenter="NA"
   authors="Jim-Parker"
   manager="jwhit"
   editor="" />
<tags  ms.service="backup" ms.devlang="NA" ms.topic="article" ms.tgt_pltfrm="NA" ms.workload="TBD" ms.date="09/16/2015" ms.author="trinadhk";"jimpark"; "aashishr" />


I limiti seguenti si applicano ai Backup di Azure.

| Identificatore limite | Limite predefinito |
|---|---|
|Numero di server/macchine che possono essere registrati con ogni insieme di credenziali|50 per Windows Server/Client/SCDPM <br/> 100 per le macchine virtuali IaaS|
|Dimensioni di un'origine dati per i dati archiviati nel servizio di archiviazione Azure insieme di credenziali|54400 GB max<sup>1</sup>|
|Numero di insiemi di credenziali di backup creati in ogni sottoscrizione di Azure|25|
|Numero di volte in cui è possibile pianificare backup al giorno|3 al giorno per Windows Server/Client<br/>2 al giorno per SCDPM<br/> Una volta al giorno per le macchine virtuali IaaS|
|Dischi dati collegati a una macchina virtuale di Azure per il backup|16|

- <sup>1</sup>Il limite di 54400 GB non si applica ai backup delle macchine virtuali IaaS.

<!---HONumber=Sept15_HO3-->