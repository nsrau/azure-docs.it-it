
<properties
    pageTitle="Non archiviare mai dati sensibili in immagini personalizzate per Azure RemoteApp | Microsoft Azure"
    description="Informazioni sulle linee guida per l'archiviazione dei dati in immagini personalizzate in Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# Non archiviare mai dati sensibili in immagini personalizzate

> [AZURE.IMPORTANT]
Azure RemoteApp sta per essere sospeso. Per i dettagli, vedere l'[annuncio](https://go.microsoft.com/fwlink/?linkid=821148).

Quando si ospita un'applicazione personalizzata in Azure RemoteApp, il primo passaggio consiste nella creazione di un'immagine personalizzata. L'immagine personalizzata viene usata per creare istanze di VM che forniscono le app agli utenti. L'immagine personalizzata deve contenere SOLTANTO applicazioni e mai dati sensibili che possano essere persi, ad esempio database SQL, file personali o file di dati speciali come i file aziendali QuickBooks. Tutti i dati riservati devono essere esterni ad Azure RemoteApp e trovarsi in un file server, in un'altra VM di Azure o in SQL Azure. L'immagine deve ospitare soltanto l'applicazione che si collega all'origine dati e visualizza i dati. Per altre informazioni vedere [Requisiti per le immagini di Azure RemoteApp](remoteapp-imagereqs.md).

Per comprendere il motivo per cui non si devono archiviare dati sensibili, è necessario comprendere il funzionamento di Azure RemoteApp. Quando si crea o si aggiorna una raccolta, sullo sfondo vengono creati più cloni o copie dell'immagine. Tutte le istanze di VM sono repliche esatte dell'immagine personalizzata. Quando gli utenti avviano le applicazioni, si collegano a una di queste istanze di VM. Non è però garantita la stessa istanza e l'istanza non è importante perché non è permanente. Le istanze di VM che ospitano le applicazioni non sono permanenti e possono essere eliminate o distrutte, ad esempio durante l'aggiornamento della raccolta.

Quando viene eseguito il provisioning della raccolta e gli utenti si collegano alle VM, i dati utente sono permanenti e protetti perché vengono salvati in una memoria separata all'interno di un disco rigido virtuale definito [disco profili utente](remoteapp-upd.md), ovvero il profilo utente in c:\\users<userprofile>. Quando si avvia un'applicazione, il disco profili utente viene montato e gestito come un profilo utente locale dal sistema operativo. Altre informazioni su come [Azure RemoteApp salva dati e impostazioni](remoteapp-upd.md).

Dati di esempio che non devono trovarsi nell'immagine:

- Dati condivisi per l'accesso degli utenti
- SQL DB o QuickBooks DB
- Tutti i dati in D:\\

Dati di esempio che possono trovarsi nel profilo predefinito da copiare nel disco profili utente di ogni utente:

- File di configurazione di ogni utente
- Script che gli utenti devono mantenere nel loro disco profili utente

Punti principali:

- Quando si crea un'immagine personalizzata, non archiviare mai nell'immagine dati sensibili che possano andare perduti.
- I dati sensibili devono sempre trovarsi in un file server separato, in una VM di Azure separata o nel cloud e devono essere sempre esterni alle istanze della VM che ospita le applicazioni all'interno di Azure RemoteApp.
- I dati utente vengono salvati e mantenuti nel disco profili utente.

<!---HONumber=AcomDC_0817_2016-->