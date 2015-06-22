
<properties 
    pageTitle="Requisiti delle app per RemoteApp"
    description="Informazioni sui requisiti per le app che si desidera usare in RemoteApp" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/11/2015" 
    ms.author="elizapo" />



# Requisiti delle app
RemoteApp supporta applicazioni basate su Windows a 32 bit o a 64 bit in streaming Windows da un'installazione di Windows Server 2012 R2. La maggior parte delle applicazioni basate su Windows a 32 bit o a 64 bit vengono eseguite "così come sono" in un ambiente RemoteApp (Servizi Desktop remoto, in precedenza noti come Servizi terminal). Tuttavia, esiste una differenza tra esecuzione e corretta esecuzione: alcune applicazioni funzionano correttamente e offrono buone prestazioni, mentre altre no. Le informazioni seguenti forniscono indicazioni per lo sviluppo di applicazioni in un ambiente di Servizi Desktop remoto e l'esecuzione di test per garantire la compatibilità.

Suggerimento: è in corso la creazione di alcuni esempi di app per gli utenti. Entro la fine del mese verranno pubblicati altri argomenti sull'uso di Microsoft Access, QuickBooks e App-V in RemoteApp.

## Requisiti
Questi tre requisiti, se seguiti, contribuiscono a una corretta esecuzione dell'applicazione in RemoteApp: 

1.	Le applicazioni che soddisfano tutti i [requisiti di certificazione per le app desktop di Windows](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx) e sono conformi alle [linee guida di programmazione dei Servizi Desktop remoto](https://msdn.microsoft.com/library/aa383490.aspx) saranno totalmente compatibili con RemoteApp. 
2.	Le applicazioni non devono mai archiviare dati localmente nell'immagine o nelle istanze di RemoteApp che possono essere perse.  Dopo aver creato una raccolta di RemoteApp, le istanze vengono clonate e sono senza stato; inoltre, devono contenere solo applicazioni. Archiviare i dati in un'origine esterna o all'interno del profilo utente. 
3.	Le immagini personalizzate non devono mai contenere dati che possono essere persi.  

## Test delle app
Usare questa procedura per eseguire il test delle applicazioni:

1.	Installare Windows Server 2012 R2 e l'applicazione
2.	Abilitare Desktop remoto
3.	Creare due account utente, Utente A e Utente B, aggiungendo entrambi gli account utente al gruppo di sicurezza di Desktop remoto. 
4.	Verificare la compatibilità multisessione stabilendo due sessioni di Servizi Desktop remoto simultanee al PC durante l'avvio dell'applicazione.
5.	Convalidare il comportamento dell'app

## Linee guida sullo sviluppo di applicazioni
Usare le linee guida seguenti per lo sviluppo di applicazioni per RemoteApp. 

### Più utenti
 
- L'installazione di un'[applicazione per un singolo utente](https://msdn.microsoft.com/library/aa380661.aspx)può creare problemi in un ambiente multiutente. 
- Le applicazioni devono [archiviare le informazioni specifiche dell'utente](https://msdn.microsoft.com/library/aa383452.aspx) in percorsi specifici dell'utente, separatamente da informazioni globali che si applicano a tutti gli utenti. 
- RemoteApp usa più [spazi dei nomi per gli oggetti del kernel](https://msdn.microsoft.com/library/aa382954.aspx); uno spazio dei nomi globale viene usato principalmente dai servizi nelle applicazioni client/server. 
- Non è opportuno presupporre che il nome del computer o l'[indirizzo IP](https://msdn.microsoft.com/library/aa382942.aspx) assegnato al computer sia associato a un singolo utente poiché più utenti possono accedere contemporaneamente a un server Host sessione Desktop remoto. 

### Prestazioni
- Disabilitare gli [effetti grafici](https://msdn.microsoft.com/library/aa380822.aspx) prima di aggiungere l'app a RemoteApp.
- Per ottimizzare la disponibilità della CPU per tutti gli utenti, disabilitare le [attività in background ](https://msdn.microsoft.com/library/aa380665.aspx) o creare attività in background efficienti che non richiedano molte risorse. 
- È consigliabile ottimizzare e bilanciare l'[utilizzo del thread](https://msdn.microsoft.com/library/aa383520.aspx) dell'applicazione per un ambiente multiutente e multiprocessore.
- Per ottimizzare le prestazioni, è consigliabile che le applicazioni [rilevino](https://msdn.microsoft.com/library/aa380798.aspx) se sono in esecuzione in una sessione client. 


<!--HONumber=52--> 