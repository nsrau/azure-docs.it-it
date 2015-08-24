
<properties 
    pageTitle="Novità di RemoteApp di Azure"
    description="Informazioni sulle modifiche e i miglioramenti apportati a RemoteApp di Azure" 
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
    ms.date="08/12/2015" 
    ms.author="elizapo" />



# Novità di RemoteApp

Uno dei vantaggi di RemoteApp è che viene costantemente migliorato. Ogni modifica apportata viene comunicata qui.

## 2015 luglio 2014

A luglio vengono portati in scena le novità in arrivo nel mese di agosto, pertanto non c’è molto di cui parlare di ora, principalmente aggiornamenti dei documenti. Ecco le modifiche più recenti:

- Aggiunta della scheda **Supporto** al portale, in modo da poter accedere più facilmente alle risorse di supporto, ad esempio i forum.
- Rielaborazione delle informazioni sulla risoluzione dei problemi per la creazione di una raccolta ibrida. Esaminare [le ultimissime novità](remoteapp-hybridtrouble.md) per suggerimenti sulla risoluzione dei problemi, come identificare le porte appropriate per configurare la rete VNET.
- Documentato come i [dati utente](remoteapp-upd.md) vengono creati e salvati in Azure RemoteApp.
- Documentato come [bloccare le applicazioni](remoteapp-secure.md).
- Pubblicati i [cmdlet di Azure RemoteApp](https://msdn.microsoft.com/library/mt428031.aspx).
- Infine, abbiamo iniziato una conversazione con alcuni utenti di Azure RemoteApp sulla terminologia. Cercare le modifiche al modo in cui si fa riferimento alle diverse opzioni della raccolta.

## Giugno 2015

Sono state introdotte numerose modifiche. Il team è stato molto occupato nel mese di giugno:

- La [pagina di destinazione](https://www.remoteapp.windowsazure.com/) di RemoteApp di Azure è stata riprogettata. 
- Il software di tutte le immagini disponibili come parte della sottoscrizione è stato aggiornato.
- Sono stati apportati miglioramenti alle raccolte ibride, tra cui il supporto del tunneling forzato e il controllo della dimensione della subnet IP prima di tentare di creare la raccolta.
- Rilevato che il carattere jolly * non funziona per le webcam. In alternativa, è necessario specificare l'ID di istanza o GUID. Microsoft aggiornerà le informazioni sul reindirizzamento di conseguenza
- per consentire l’aggiunta di software antivirus all’immagine quando si crea un'immagine modello dalla raccolta di Azure.

Altre modifiche saranno distribuite nel mese di luglio, per cui sarà presto disponibile un nuovo aggiornamento.

## Maggio 2015

Sono state introdotte diverse aggiunte e sono trascorsi diversi mesi dopo la creazione iniziale di questo argomento, pertanto questo elenco non è esaustivo. Provare queste nuove funzionalità:

- Automatizzare tutto - RemoteApp di Azure ora ha [cmdlet nel modulo Azure PowerShell](remoteapp-tutorial-arawithpowershell.md). 
- [Creare un'immagine di RemoteApp di Azure da una macchina virtuale di Azure](remoteapp-image-on-azurevm.md). Consente di caricare l'immagine personalizzata in Azure molto più rapidamente.
- Utilizzare una rete virtuale di Azure invece di una rete virtuale RemoteApp per connettersi alle risorse di rete aziendale in Azure. Sono state aggiornate le [istruzioni sulle raccolte ibride](remoteapp-create-hybrid-deployment.md) per illustrare nei dettagli la creazione di una rete virtuale di Azure (Passaggio 1).
- Parlando di reti virtuali, consultare le [nuove informazioni](remoteapp-vnetsizing.md) su limiti e limitazioni delle dimensioni delle reti virtuali.
- Parlando di limiti, quali sono i [limiti dei servizi e le impostazioni predefinite](remoteapp-servicelimits.md)?

Per altre informazioni su Azure RemoteApp Il team di RemoteApp era al lavoro presso Ignite alcune settimane fa. Guardare il video di Eric, [The Fundamentals of Microsoft Azure RemoteApp Management and Administration](http://channel9.msdn.com/Events/Ignite/2015/BRK3868).

Si desidera vedere RemoteApp di Azure nel mondo reale? Consultare l’esercitazione [Eseguire qualsiasi applicazione su qualsiasi dispositivo in qualsiasi luogo](remoteapp-anyapp.md), che illustra come condividere l'accesso con gli utenti, inclusa la condivisione di file di database. È disponibile anche un’esercitazione su come fare in modo che [Office 365](remoteapp-tutorial-o365anywhere.md) funzioni allo stesso modo su ogni dispositivo.

Grazie per l’attenzione. Ulteriori aggiornamenti saranno comunicati il mese prossimo.

<!---HONumber=August15_HO7-->