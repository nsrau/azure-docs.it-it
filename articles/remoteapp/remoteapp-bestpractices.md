<properties
    pageTitle="Procedure consigliate di Azure RemoteApp | Microsoft Azure"
    description="Procedure consigliate per la configurazione e l'uso di Azure RemoteApp."
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
    ms.date="06/13/2016"
    ms.author="elizapo" />

# Procedure consigliate per la configurazione e l'uso di Azure RemoteApp

Le informazioni seguenti consentono di configurare e usare in modo efficiente Azure RemoteApp.

## Connettività


- Usare sempre la versione più recente del client. L'uso di client meno recenti potrebbe causare problemi di connettività e altre situazioni di funzionalità ridotta. Per assicurarsi che sia sempre installato il client più recente, abilitare gli aggiornamenti automatici delle applicazioni per il dispositivo.
- Usare sempre la connessione internet più stabile e affidabile a disposizione.  
- Usare solo connessioni proxy supportate per prestazioni di connettività ottimali. Il proxy SOCKS non è supportato.

## Applicazioni


- Salvare e chiudere le applicazioni RemoteApp al termine dell'uso dell'applicazione. La mancata chiusura dell'applicazione potrebbe comportare perdite di dati.
- Convalidare le applicazioni personalizzate prima di usarle in Azure RemoteApp. Questo include assicurarsi che funzionino su una piattaforma multisessione e che non consumino inutilmente risorse come memoria e CPU causando possibili degradi delle prestazioni per altri utenti nella stessa raccolta. Per informazioni, scaricare e leggere il documento relativo alle [procedure consigliate per la compatibilità delle applicazioni per Servizi Desktop remoto](http://www.microsoft.com/download/details.aspx?id=18704).

## Configurazione e gestione


- Mantenere le immagini modello aggiornate e installare aggiornamenti software e altre correzioni critiche in base alle esigenze. Ciò garantisce che man mano che Azure RemoteApp si ridimensiona automaticamente per soddisfare la capacità richiesta, ogni istanza viene correttamente aggiornata.  
- Assicurarsi che la distribuzione di Active Directory Federation Services (ADFS) sia protetta e affidabile. In caso contrario le autenticazioni client potrebbero non riuscire, impedendo agli utenti di accedere ad Azure RemoteApp.
- Configurare immagini modello con le applicazioni, i ruoli o le funzionalità installate, in modo che siano prive di stato. È consigliabile non basarsi su alcuna istanza di macchine virtuali in un servizio di RemoteApp che si trova in uno stato persistente.
	- Archiviare tutti i dati utente in profili utente o in altri percorsi di archiviazione esterni al servizio, ad esempio condivisioni file locali o OneDrive.
	- Archiviare i dati condivisi in percorsi di archiviazione esterni al servizio, ad esempio condivisioni file locali o OneDrive.
	- Configurare le impostazioni a livello di sistema nell'immagine modello anziché sulle singole macchine virtuali in un servizio.
	- Disabilitare gli aggiornamenti software automatici per le applicazioni pubblicate. Applicarli invece manualmente per l'immagine modello e testarli prima della distribuzione dal modello.

<!---HONumber=AcomDC_0622_2016-->