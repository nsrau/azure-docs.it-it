<properties
   pageTitle="Come integrare OneDrive for Business e Azure RemoteApp | Microsoft Azure"
   description="Informazioni su come usare OneDrive for Business con Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="pavithir"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="01/13/2016"
   ms.author="elizapo"/>

# Come integrare OneDrive for Business e Azure RemoteApp

OneDrive for Business può essere usato come archivio di file con Azure RemoteApp. OneDrive for Business è un'ottima soluzione per mantenere i file sincronizzati tra tutti i dispositivi e le aree di lavoro. Il [disco del profilo utente](remoteapp-upd.md) (UPD) di un utente è una posizione in cui si possono archiviare i file per le app di Azure RemoteApp, che però sono accessibili solo con Azure RemoteApp. OneDrive for Business, invece, consente all'utente di accedere ai file ovunque e ogni volta che si rende necessario, senza il prerequisito di usare Azure RemoteApp. Questo articolo illustra le versioni di OneDrive for Business supportate e i vari modi in cui gli amministratori possono configurare OneDrive for Business per Azure RemoteApp.

## Versioni di OneDrive supportate

OneDrive è disponibile in due versioni: OneDrive e OneDrive for Business. Solo OneDrive for Business è supportato in Azure RemoteApp. OneDrive personale funziona, ma non è supportato ufficialmente. Inoltre, solo la versione più recente di OneDrive for Business, nota anche come client di sincronizzazione di nuova generazione, è supportata in Azure RemoteApp e nei server Host sessione Desktop remoto/Citrix/Terminal.

>[AZURE.NOTE]L'edizione consumer/personale di OneDrive non è supportata in Azure RemoteApp. Non tutte le versioni di OneDrive for Business sono comunque supportate, perché non sono state certificate per l'utilizzo in Windows Server. Anche se il nuovo client, ovvero il client di sincronizzazione di nuova generazione, e le versioni precedenti di Groove funzionano apparentemente in modo corretto in Azure RemoteApp, come descritto nell'articolo all'indirizzo [https://support.microsoft.com/it-IT/kb/2965687](https://support.microsoft.com/kb/2965687), non tutte le funzionalità dei motori di sincronizzazione precedenti sono disponibili nei server Citrix/Terminal (Windows Server). Usare il nuovo client di sincronizzazione in Azure RemoteApp e in altre distribuzioni di Windows Server.

## Opzioni di configurazione disponibili per OneDrive for Business

- **Installazione tradizionale del motore di sincronizzazione di OneDrive for Business:** il client di sincronizzazione di OneDrive for Business può essere installato in uno SKU server, ad esempio Desktop remoto o una sessione di RemoteApp o di Terminal Server, e nelle cartelle selezionate per la sincronizzazione nella sessione di RemoteApp, come avviene in uno SKU client di Windows. Il percorso predefinito usato da OneDrive for Business per la sincronizzazione dei file è lo stesso percorso del disco del profilo utente usato per archiviare dati utente e impostazioni in Azure RemoteApp, ovvero C:\utenti<nomeutente>. Questo disco, così come i file ODB, segue l'utente in tutte le VM a cui l'utente accede. L'app OneDrive for Business deve essere pubblicata dall'amministratore per tutti gli utenti e questi ultimi devono avviarla in ogni nuova sessione per essere certi che il motore di sincronizzazione sia attivato. L'avvio può anche essere automatizzato con uno script di accesso. OneDrive for Business scaricherà l'intero file nella VM in cui è in esecuzione la sessione. Durante la sincronizzazione del contenuto di un utente, tutti gli elementi che ne fanno parte, come CPU/dati trasferiti/dati archiviati, richiedono un notevole carico di lavoro che non è semplicemente ottimizzato per i terminali con un numero elevato di utenti che accedono brevemente a ogni computer. Con la sincronizzazione selettiva è possibile ridurre il carico di lavoro, ma il problema persiste.
- **"Virtualizzare" OneDrive for Business/reindirizzarlo dal fat client locale alla sessione:** se si sincronizza OneDrive con una cartella su un'unità del dispositivo client, è possibile scegliere di [reindirizzare](remoteapp-redirection.md) l'unità ad Azure RemoteApp. L'unità deve essere lo stessa nei client di tutti gli utenti e OneDrive deve essere sincronizzato con una cartella di quell'unità. Se gli utenti accedono a RemoteApp da un altro client, questi file potrebbero non essere disponibili. Come soluzione alternativa, gli utenti possono comunque accedere ai file con la versione online di OneDrive. 
- **Presentare OneDrive for Business come un'unità all'interno dell'ambiente Azure RemoteApp senza memorizzazione nella cache/ sincronizzazione dei file:** eseguire il mapping dell'URL http di OneDrive for Business a un'unità nella VM. Il mapping di OneDrive for Business all'unità di rete nell'ambiente Host sessione Desktop remoto è supportato. Scenari in cui può essere usato: 
	- Quando si usano thin client, senza archiviazione locale, per accedere ad Azure RemoteApp. L'applicazione richiede che i file siano archiviati in OneDrive for Business, ma questi dovrebbero "apparire" come locali e l'amministratore non vuole sincronizzare i file con una VM.
	- Quando in OneDrive for Business sono presenti molti file di grandi dimensioni che sono stati selezionati per la sincronizzazione. Dato il carico di lavoro della sincronizzazione, è possibile che non tutti i file siano sincronizzati quando l'utente vuole usarli. Inoltre, se le dimensioni totali dei file superano 50 GB, non possono essere archiviati sul disco del profilo utente.

Negli scenari precedenti gli amministratori possono scegliere le opzioni per il mapping di un'unità nella VM all'URL http di OneDrive for Business dell'utente. Ecco alcune opzioni per abilitare il mapping:

- Avere file binari di Office nell'immagine e non attivare il motore di sincronizzazione di OneDrive for Business.
- NON avere file binari di Office nell'immagine. In questo caso la disponibilità del pacchetto Esperienza desktop è un prerequisito. In particolare, il servizio WebClient (noto anche come WebDAV) deve essere installato come parte del pacchetto Esperienza desktop. 

### Installare il pacchetto Esperienza Desktop in Windows Server 2012 R2
Per installare il pacchetto Esperienza desktop:

1. In Server Manager fare clic su **Gestione > Aggiungi ruoli e funzionalità**.
2. Fare clic su **Funzionalità** e quindi su **Interfacce utente e infrastruttura -> Esperienza desktop**.

### Eseguire il mapping all'URL di OneDrive for Business

Seguire le istruzioni nell'articolo di supporto all'indirizzo 
[https://support.microsoft.com/kb/2616712](https://support.microsoft.com/kb/2616712)
 
Un passaggio importante dell'installazione consiste nel verificare che sia selezionato **Mantieni l'accesso**.

A grandi linee ecco le istruzioni:

1.	Individuare l'URL dell'unità. L'URL usato per il mapping dell'unità è quello che viene visualizzato quando si passa alla home directory in OneDrive for Business online. Ad esempio:
 
	https://microsoft-my.sharepoint.com/personal/alias_microsoft_com/_layouts/15/onedrive.aspx?AjaxDelta=1&isStartPlt1=something
2.	Aprire l'URL con un browser nella sessione di RemoteApp e selezionare **Mantieni l'accesso** prima di accedere all'URL dell'account.
3.	Aprire Esplora risorse ed eseguire il mapping di un'unità all'URL precedente. Se l'URL non viene risolto, è possibile usare la forma abbreviata:
	
	https://microsoft-my.sharepoint.com/personal/alias_microsoft_com. 

	Viene creata immediatamente l'unità mappata, che sarà simile alla seguente:
 
	![OneDrive for Business come un'unità di rete mappata](./media/remoteapp-onedrive/ra-mappeddrive.png)

<!---HONumber=AcomDC_0121_2016-->