
<properties 
    pageTitle="Caricare un'immagine personalizzata per RemoteApp di Azure"
    description="Informazioni su come caricare un'immagine personalizzata per RemoteApp di Azure" 
    services="remoteapp" 
	documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/28/2015" 
    ms.author="ericor" />



# Caricare un'immagine personalizzata per RemoteApp di Azure

Dopo aver creato o aggiornato con le modifiche l'immagine modello personalizzata, è necessario caricare l'immagine nella raccolta immagini di Azure RemoteApp. Usare i passaggi seguenti.


## Prima di iniziare

1.      Verificare che l'immagine personalizzata soddisfi i [requisiti per l'immagine](remoteapp-imagereqs.md) e i [requisiti per l'applicazione](remoteapp-appreqs.md).
2.      Installare il [modulo Azure PowerShell](../install-configure-powershell.md).

## Istruzioni dettagliate su come caricare un'immagine personalizzata

1.      Aprire il portale di gestione di Azure e passare alla pagina RemoteApp.
2.      Nella scheda **Immagini modello** fare clic su **Carica** nella parte inferiore della pagina.
4.      Immettere un nome descrittivo per l'immagine e specificare il percorso dell'account di archiviazione. Verificare che il percorso sia uguale a quello della raccolta RemoteApp oppure che sia il percorso dove verrà creata una nuova raccolta. 
5.      Quando richiesto, scaricare lo script nel PC locale.
6.      Copiare negli Appunti i parametri del comando contenuti nella casella di testo.
7.      Aprire una finestra di Windows PowerShell con privilegi elevati  
8.      Dalla finestra di Windows PowerShell con privilegi elevati passare alla directory in cui è stato scaricato lo script.
9.      Incollare il comando copiato e premere **Invio**.

	Viene avviato il processo di caricamento, la cui durata dipende da diversi fattori, ad esempio la velocità di rete e le dimensioni dell'immagine

11.    Se il caricamento non riesce a causa di interruzioni di rete o problemi simili, è possibile riprendere il processo di caricamento avviato in qualsiasi momento. Per riprendere il caricamento, eseguire di nuovo lo script usando la stessa riga di comando.

> [AZURE.WARNING]Non modificare mai lo script di caricamento. Sono stati implementati controlli specifici che assicurano che l'immagine soddisfi i requisiti per l'immagine e per l'applicazione.

## Problemi frequenti

- Assicurarsi di usare Windows PowerShell, e non Azure PowerShell. È necessario installare il modulo Azure PowerShell perché alcuni moduli sono necessari durante il processo di caricamento. 
- Non modificare in nessun caso lo script. Le convalide sono state applicate per semplificare l'utilizzo da parte degli utenti.
- Se il file VHD si blocca durante il caricamento, copiare il file o spostarlo in un nuovo percorso e riprovare il caricamento. Il caricamento potrebbe essere ostacolato da qualche processo di Windows in esecuzione.  
 

<!---HONumber=July15_HO4-->